<template>
  <div>
    <h1>{{ name }}</h1>

    <div v-if="pendingProjects || pendingBlogs">Loading...</div>

    <h1>Projects</h1>

    <ul>
      <li v-for="project in projects" :key="project.id">
        <h2>{{ project.name }}</h2>
        <p>{{ project.description }}</p>
      </li>
    </ul>

    <div>
      <h1>Create a new project</h1>
      <form @submit.prevent="createProject">
        <div>
          <label for="createName">Project Name:</label>
          <input type="text" id="createName" v-model="newProject.name" required />
        </div>
        <div>
          <label for="createDescription">Description:</label>
          <textarea
            id="createDescription"
            v-model="newProject.description"
            required
          ></textarea>
        </div>
        <button type="submit">Create Project</button>
      </form>
    </div>

    <div>
      <h1>Update a project</h1>
      <form @submit.prevent="updateProject">
        <div>
          <label for="updateId">Project ID:</label>
          <input type="text" id="updateId" v-model="updateProjectData.id" required />
        </div>
        <div>
          <label for="updateName">Project Name:</label>
          <input type="text" id="updateName" v-model="updateProjectData.name" required />
        </div>
        <div>
          <label for="updateDescription">Description:</label>
          <textarea
            id="updateDescription"
            v-model="updateProjectData.description"
            required
          ></textarea>
        </div>
        <button type="submit">Update Project</button>
      </form>
    </div>

    <div>
      <h1>Delete a project</h1>
      <form @submit.prevent="deleteProject">
        <div>
          <label for="deleteId">Project ID:</label>
          <input type="text" id="deleteId" v-model="deleteProjectData.id" required />
        </div>
        <button type="submit">Delete Project</button>
      </form>
    </div>

    <div>
      <h1>Blogs</h1>
      <ul>
        <li v-for="blog in blogs" :key="blog.id">
          <h2>{{ blog.title }}</h2>
          <p>{{ blog.content }}</p>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const name = "Jalina Hirushan";

const {
  data: projects,
  pending: pendingProjects,
  error: errorProjects,
} = useFetch("http://localhost:5000/projects");

const {
  data: blogs,
  pending: pendingBlogs,
  error: errorBlogs,
} = useFetch("http://localhost:5000/blogs");

const newProject = ref({
  name: "",
  description: "",
});

const updateProjectData = ref({
  id: "",
  name: "",
  description: "",
});

const deleteProjectData = ref({
  id: ""
});

const fetchProjects = async () => {
  // Re-fetch the projects
  const response = await fetch("http://localhost:5000/projects");
  projects.value = await response.json();
};

const createProject = async () => {
  try {
    const response = await fetch("http://localhost:5000/projects", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(newProject.value),
    });

    if (!response.ok) {
      throw new Error("Failed to create project");
    }

    await fetchProjects(); // Refresh the list of projects

    newProject.value.name = "";
    newProject.value.description = "";
  } catch (error) {
    console.error("Error:", error);
  }
};

const updateProject = async () => {
  try {
    const response = await fetch(`http://localhost:5000/projects/${updateProjectData.value.id}`, {
      method: "PATCH",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(updateProjectData.value),
    });

    if (!response.ok) {
      throw new Error("Failed to update project");
    }

    await fetchProjects(); // Refresh the list of projects

    updateProjectData.value.id = "";
    updateProjectData.value.name = "";
    updateProjectData.value.description = "";
  } catch (error) {
    console.error("Error:", error);
  }
};

const deleteProject = async () => {
  try {
    const response = await fetch(`http://localhost:5000/projects/${deleteProjectData.value.id}`, {
      method: "DELETE",
      headers: {
        "Content-Type": "application/json",
      }
    });

    if (!response.ok) {
      throw new Error("Failed to delete project");
    }

    await fetchProjects(); // Refresh the list of projects

    deleteProjectData.value.id = "";
  } catch (error) {
    console.error("Error:", error);
  }
};
</script>

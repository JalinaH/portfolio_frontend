<template>
  <h1 class="title">Hello I'm {{ name }}</h1>

  <div class="root-section">
    <div v-if="pendingProjects || pendingBlogs">Loading...</div>

    <div class="section project-section">
      <h1 class="section-title">Projects</h1>

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
            <input
              type="text"
              id="createName"
              v-model="newProject.name"
              required
            />
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
            <label for="updateId">Select the project name to update:</label>
            <select id="updateId" v-model="updateProjectData.id" required>
              <option value="" disabled>Select a project</option>
              <option
                v-for="project in projects"
                :key="project._id"
                :value="project._id"
              >
                {{ project.name }}
              </option>
            </select>
          </div>
          <div>
            <label for="updateName">Project Name:</label>
            <input
              type="text"
              id="updateName"
              v-model="updateProjectData.name"
              required
            />
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
            <label for="updateId">Select the project name to delete:</label>
            <select id="updateId" v-model="deleteProjectData.id" required>
              <option value="" disabled>Select a project</option>
              <option
                v-for="project in projects"
                :key="project._id"
                :value="project._id"
              >
                {{ project.name }}
              </option>
            </select>
          </div>
          <button type="submit">Delete Project</button>
        </form>
      </div>
    </div>

    <div class="section blog-section">
      <h1 class="section-title">Blogs</h1>
      <ul>
        <li v-for="blog in blogs" :key="blog.id">
          <h2>{{ blog.title }}</h2>
          <p>{{ blog.content }}</p>
        </li>
      </ul>

      <div>
        <h1>Create a new blog</h1>
        <form @submit.prevent="CreateBlog">
          <div>
            <label for="createTitle">Blog Title:</label>
            <input
              type="text"
              id="createTitle"
              v-model="newBlog.title"
              required
            />
          </div>
          <div>
            <label for="createContent">Content:</label>
            <textarea
              id="createContent"
              v-model="newBlog.content"
              required
            ></textarea>
          </div>
          <button type="submit">Create Blog</button>
        </form>
      </div>

      <div>
        <h1>Update a blog</h1>
        <form @submit.prevent="UpdateBlog">
          <div>
            <label for="updateId">Select the blog name to update:</label>
            <select id="updateId" v-model="updateBlogData.id" required>
              <option value="" disabled>Select a blog</option>
              <option v-for="blog in blogs" :key="blog._id" :value="blog._id">
                {{ blog.title }}
              </option>
            </select>
          </div>
          <div>
            <label for="updateTitle">Blog Title:</label>
            <input
              type="text"
              id="updateTitle"
              v-model="updateBlogData.title"
              required
            />
          </div>
          <div>
            <label for="updateContent">Content:</label>
            <textarea
              id="updateContent"
              v-model="updateBlogData.content"
              required
            ></textarea>
          </div>
          <button type="submit">Update Blog</button>
        </form>
      </div>

      <div>
        <h1>Delete a blog</h1>
        <form @submit.prevent="DeleteBlog">
          <div>
            <label for="updateId">Select the blog name to delete:</label>
            <select id="updateId" v-model="deleteBlogData.id" required>
              <option value="" disabled>Select a blog</option>
              <option v-for="blog in blogs" :key="blog._id" :value="blog._id">
                {{ blog.title }}
              </option>
            </select>
          </div>
          <button type="submit">Delete Blog</button>
        </form>
      </div>
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
  id: "",
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

    const result = await response.json();
    projects.value.push(result);

    newProject.value.name = "";
    newProject.value.description = "";
  } catch (error) {
    console.error("Error:", error);
  }
};

const updateProject = async () => {
  try {
    const response = await fetch(
      `http://localhost:5000/projects/${updateProjectData.value.id}`,
      {
        method: "PATCH",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(updateProjectData.value),
      }
    );

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
    const response = await fetch(
      `http://localhost:5000/projects/${deleteProjectData.value.id}`,
      {
        method: "DELETE",
        headers: {
          "Content-Type": "application/json",
        },
      }
    );

    if (!response.ok) {
      throw new Error("Failed to delete project");
    }

    await fetchProjects(); // Refresh the list of projects

    deleteProjectData.value.id = "";
  } catch (error) {
    console.error("Error:", error);
  }
};

const newBlog = ref({
  title: "",
  content: "",
});

const updateBlogData = ref({
  id: "",
  title: "",
  content: "",
});

const deleteBlogData = ref({
  id: "",
});

const fetchBlogs = async () => {
  // Re-fetch the blogs
  const response = await fetch("http://localhost:5000/blogs");
  blogs.value = await response.json();
};

const CreateBlog = async () => {
  try {
    const response = await fetch("http://localhost:5000/blogs", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(newBlog.value),
    });

    if (!response.ok) {
      throw new Error("Failed to create blog");
    }

    const result = await response.json();
    blogs.value.push(result);

    newBlog.value.title = "";
    newBlog.value.content = "";
  } catch (error) {
    console.error("Error:", error);
  }
};

const UpdateBlog = async () => {
  try {
    const response = await fetch(
      `http://localhost:5000/blogs/${updateBlogData.value.id}`,
      {
        method: "PATCH",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(updateBlogData.value),
      }
    );

    if (!response.ok) {
      throw new Error("Failed to update blog");
    }

    await fetchBlogs(); // Refresh the list of blogs

    updateBlogData.value.id = "";
    updateBlogData.value.title = "";
    updateBlogData.value.content = "";
  } catch (error) {
    console.error("Error:", error);
  }
};

const DeleteBlog = async () => {
  try {
    const response = await fetch(
      `http://localhost:5000/blogs/${deleteBlogData.value.id}`,
      {
        method: "DELETE",
        headers: {
          "Content-Type": "application/json",
        },
      }
    );

    if (!response.ok) {
      throw new Error("Failed to delete blog");
    }

    await fetchBlogs(); // Refresh the list of blogs

    deleteBlogData.value.id = "";
  } catch (error) {
    console.error("Error:", error);
  }
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&display=swap");

* {
  font-family: "Poppins", sans-serif;
}

.root-section {
  display: flex;
  justify-content: space-between;
  padding: 20px;
}

.section {
  width: 48%;
}

.title {
  color: black;
  font-size: 48px;
  font-weight: bold;
  margin-bottom: 20px;
  text-align: center;
}

.section-title {
  color: black;
  font-size: 36px;
  font-weight: bold;
  margin-bottom: 20px;
  text-align: center;
}

form {
  margin-top: 20px;
}

label {
  display: block;
  font-size: 18px;
  margin-bottom: 5px;
}

input,
textarea,
select {
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 16px;
  margin-bottom: 10px;
  padding: 10px;
  width: 100%;
}

button {
  background-color: #4caf50;
  border: none;
  border-radius: 5px;
  color: white;
  cursor: pointer;
  font-size: 16px;
  margin-top: 10px;
  padding: 10px;
  width: 100%;
}

button:hover {
  background-color: #45a049;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  margin-bottom: 10px;
  padding: 10px;
}
</style>

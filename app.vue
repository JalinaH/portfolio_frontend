<template>
  <div>
    <h1>{{ name }}</h1>

    <div v-if="pending">Loading...</div>

    <h1>Projects</h1>

    <ul>
      <li v-for="project in projects" :key="project.name">
        <h2>{{ project.name }}</h2>
        <p>{{ project.description }}</p>
      </li>
    </ul>
  </div>
  <div>
    <h1>Blogs</h1>
    <ul>
      <li v-for="blog in blogs" :key="blog.title">
        <h2>{{ blog.title }}</h2>
        <p>{{ blog.content }}</p>
      </li>
    </ul>
  </div>

  <div>
    <h1>Create a new project</h1>
    <form @submit.prevent="CreateProject">
      <div>
        <label for="name">Project Name:</label>
        <input type="text" id="name" v-model="newProject.name" required />
      </div>
      <div>
        <label for="description">Description:</label>
        <textarea id="description" v-model="newProject.description" required></textarea>
      </div>
      <button type="submit">Create Project</button>
    </form>
  </div>
</template>

<script setup>
const name = "Jalina Hirushan";

const newProject = ref({
  name: "",
  description: "",
});

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

</script>

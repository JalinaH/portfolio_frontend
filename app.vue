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
</template>

<script setup>
const name = "Jalina Hirushan";
const {
  data: projects,
  pending,
  error,
} = useFetch("http://localhost:5000/projects");

</script>

<template>
  <v-table>
    <thead>
      <tr>
        <th class="text-center">
          <h3>Nombre</h3>
        </th>
        <th class="text-center">
          <h3>Titulo</h3>
        </th>
        <th class="text-center">
          <h3>Contenido</h3>
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(item, index) in informacion" :key="index">
        <td class="text-center">{{ item.Nombre }}</td>
        <td class="text-center">{{ item.info.Titulo }}</td>
        <td class="text-justify">{{ item.info.Contenido }}</td>
      </tr>
    </tbody>
  </v-table>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const informacion = ref([]);

async function fetchData() {
  const usersResponse = await fetch('https://jsonplaceholder.typicode.com/users');
  const usersData = await usersResponse.json();

  const postsResponse = await fetch('https://jsonplaceholder.typicode.com/posts');
  const postsData = await postsResponse.json();

  informacion.value = [];

  usersData.forEach(user => {
    const userPosts = postsData.filter(post => post.userId === user.id);
    userPosts.forEach(post => {
      informacion.value.push({
        Nombre: user.name,
        info: {
          Titulo: post.title,
          Contenido: post.body,
        },
      });
    });
  });

  console.log(informacion.value);
}

onMounted(() => {
  fetchData();
});
</script>

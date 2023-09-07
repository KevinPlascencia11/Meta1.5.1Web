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
      <template v-for="(item, index) in informacion" :key="item.id">
        <tr v-for="(info, idx) in item.info" :key="idx">
          <td class="text-center">{{ idx === 0 ? item.Nombre : '' }}</td>
          <td class="text-center">{{ info.Titulo }}</td>
          <td class="text-justify">{{ info.Contenido }}</td>
        </tr>
      </template>
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

  informacion.value = usersData.map(user => {
    const userPosts = postsData.filter(post => post.userId === user.id);
    return {
      Nombre: user.name,
      info: userPosts.map(post => {
        return {
          Titulo: post.title,
          Contenido: post.body,
        };
      }),
    };
  });

  console.log(informacion.value);
}

onMounted(() => {
  fetchData();
});
</script>
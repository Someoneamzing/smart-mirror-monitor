<template>
  <div class="home">
    <input type="url" v-model="hostIP">
    <router-link :to="{ path: `/connect`, query: {host: hostIP} }">Connect</router-link>
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: 'Home',
  data() {
    return {
      hostIP: 'http://localhost',
    };
  },
};
</script>

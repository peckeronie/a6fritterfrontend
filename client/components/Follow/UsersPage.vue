<template>
    <main>
    
        <header>
          <h2>Users on Fritter</h2>
        </header>
  
      <section>
        <section
          v-if="$store.state.users.length"
        >
          <FollowComponent
            v-for="user in $store.state.users"
            :key="user.id"
            :user="user"
          />
        </section>
        <article
          v-else
        >
          <h3>No users found.</h3>
        </article>
      </section>
    </main>
  </template>
  
  <script>
  import FollowComponent from '@/components/Follow/FollowComponent.vue';
  
  export default {
    name: 'UsersPage',
    components: {FollowComponent},
    created() {
        this.$store.commit('refreshUsers'); 
    }
  };
  </script>
  
  <style scoped>
  section {
    display: flex;
    flex-direction: column;
  }
  
  header, header > * {
      display: flex;
      justify-content: space-between;
      align-items: center;
  }
  
  button {
      margin-right: 10px;
  }
  
  section .scrollbox {
    flex: 1 0 50vh;
    padding: 3%;
    overflow-y: scroll;
  }
  </style>
  
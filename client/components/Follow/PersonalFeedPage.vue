<!-- Default page that also displays freets -->

<template>
    <main>
      <section v-if="$store.state.username">
        <header>
          <h2>Freets of accounts being followed by @{{ $store.state.username }}</h2>
        </header>
        <CreateFreetForm />
      </section>
      <section v-else>
        <header>
          <h2>Login to view your personalized feed!</h2>
        </header>
        <article>
          <h3>
            <router-link to="/login">
              Sign in
            </router-link>
          </h3>
        </article>
      </section>
      <section>
        <section
          v-if="$store.state.username"
        >
          <FreetComponent
            v-for="freet in $store.state.feedfreets"
            :key="freet.id"
            :freet="freet"
          />
        </section>
        <article
          v-else
        >
          <h3>No freets found.</h3>
        </article>
      </section>
    </main>
  </template>
  
  <script>
  import FreetComponent from '@/components/Freet/FreetComponent.vue';
  
  export default {
    name: 'PersonalFeedPage', 
    components: {FreetComponent},
    created() {
        this.$store.commit('refreshFeed');
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
  
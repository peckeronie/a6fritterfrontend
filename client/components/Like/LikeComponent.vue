<template>
  <article
    class="like"
  >
    <header>
        <h3> Likes for Freet</h3>
        <div
        v-if="$store.state.username !== null"
        class="actions"
      >
       <button
        @click="likeFreet"
       >
        Like
      </button>
      <button
        @click="unlikeFreet"
       >
        Unlike
      </button>
      </div>
      <div
        v-if="$store.state.username === freet.author"
        class="actions"
      >
      <button
        @click="hideLikes"
    >
        Hide Likes from Public
    </button>
    <button
        @click="unhideLikes"
    >
        Unhide Likes from Public
    </button>
      </div>
    </header>
    <p class="info">
      {{ this.likes }}
    </p>
    <div
        v-if="$store.state.username !== null"
        class="actions"
      >
    <button
        v-if="!showing"
        @click="showLikers"
    >
        Show Likers
    </button>
    <button
        v-if="showing"
        @click="quitLikers"
    >
        Unexpand Likers
    </button>
    <section v-if="showing">
    <p>{{ this.likers }}</p>
    </section>
    </div>
    <section class="alerts">
      <article
        v-for="(status, alert, index) in alerts"
        :key="index"
        :class="status"
      >
        <p>{{ alert }}</p>
      </article>
    </section>
  </article>
</template>

<!-- <template>
    <div>
    <button
        v-if="liked"
        @click="unlikeFreet"
    >
        Unlike
    </button>
    <button
        v-if="!liked"
        @click="likeFreet"
    >
        Like
    </button>
    <p
      class="content"
    >
      Number of Likes: {{ this.likes }}
    </p>
</div>
</template> -->

<script>
export default {
  name: 'LikeComponent',
  props: {
    // Data from the stored freet
    freet: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      showing: false, // Whether or not to see the list of users who liked the freet
      likers: '',
      likes: '',
      alerts: {} // Displays success/error messages encountered during freet modification
    };
  },
  created() {
      this.getLikes();
      this.getLikers();
  },
  methods: {
    async getLikes() {
       /**
       * Get the number of likes for a freet.
       */
      const url = `/api/likes/likecount/${this.freet._id}`;
      try {
        const r = await fetch(url);
        const res = await r.json();
        this.likes = res['message']; 
        // const res = await r.json();
        // if (!r.ok) {
        //   throw new Error(res.error);
        // }
      } catch (e) {
        this.$set(this.alerts, e, 'error');
        setTimeout(() => this.$delete(this.alerts, e), 3000);
      }
    },
    async getLikers() {
       /**
       * Get the users who liked a freet.
       */
      const url = `/api/likes/likeusers/${this.freet._id}`;
      try {
        const r = await fetch(url);
        const res = await r.json();
        this.likers = res['names'] ? res['names'] : res['message']; 
      } catch (e) {
        this.$set(this.alerts, e, 'error');
        setTimeout(() => this.$delete(this.alerts, e), 3000);
      }
    },
    showLikers() {
      this.showing = true; 
    },
    quitLikers() {
      this.showing = false; 
    },
    async hideLikes() {
       /**
       * Hide the like metrics for a freet. 
       */
       const requestOptions = {
            method: 'PUT'
        };
      const url = `/api/likes/hide/${this.freet._id}`;
      try {
        const r = await fetch(url, requestOptions);
        const res = await r.json();
        if (!r.ok) {
          throw new Error(res.error);
        }
        this.getLikes();
        this.getLikers();
        this.$store.commit('alert', {
            message: 'Successfully hid likes!', status: 'success'
          });
      } catch (e) {
        this.$set(this.alerts, e, 'error');
        setTimeout(() => this.$delete(this.alerts, e), 3000);
      }
    },
    async unhideLikes() {
       /**
       * Unhide the like metrics for a freet. 
       */
       const requestOptions = {
            method: 'PUT'
        };
      const url = `/api/likes/unhide/${this.freet._id}`;
      try {
        const r = await fetch(url, requestOptions);
        const res = await r.json();
        if (!r.ok) {
          throw new Error(res.error);
        }
        this.getLikes();
        this.getLikers();
        this.$store.commit('alert', {
            message: 'Successfully unhid likes!', status: 'success'
          });
      } catch (e) {
        this.$set(this.alerts, e, 'error');
        setTimeout(() => this.$delete(this.alerts, e), 3000);
      }
    },
    unlikeFreet() {
      /**
       * Unlikes this freet.
       */
      const params = {
        method: 'DELETE',
        callback: () => {
          this.$store.commit('alert', {
            message: 'Successfully unliked freet!', status: 'success'
          });
        }
      };
      this.request(params);
    },
    likeFreet() {
      /**
       * Likes this freet.
       */
      const params = {
        method: 'PUT',
        // message: 'Successfully liked freet!',
        callback: () => {
          this.$store.commit('alert', {
            message: 'Successfully liked freet!', status: 'success'
          });
        }
      };
      this.request(params);
    },
    async request(params) {
      /**
       * Submits a request to the Like object's endpoint
       * @param params - Options for the request
       * @param params.body - Body for the request, if it exists
       * @param params.callback - Function to run if the the request succeeds
       */
      const options = {
        method: params.method, headers: {'Content-Type': 'application/json'}
      };
      if (params.body) {
        options.body = params.body;
      }

      try {
        const r = await fetch(`/api/likes/like/${this.freet._id}`, options);
        if (!r.ok) {
          const res = await r.json();
          throw new Error(res.error);
        }
        this.getLikes();
        this.getLikers();
        // this.editing = false;
        // this.$store.commit('refreshFreets');

        params.callback();
      } catch (e) {
        this.$set(this.alerts, e, 'error');
        setTimeout(() => this.$delete(this.alerts, e), 3000);
      }
    }
  }
};
</script>

<style scoped>
.like {
    border: 1px solid #111;
    padding: 20px;
    position: relative;
}
</style>
<template>
    <article
      class="sources"
    >
      <header>
          <h3> Sources for Freet</h3>
         
        <div
          v-if="$store.state.username !== null"
          class="actions"
        >
        <!-- <textarea
        class="content"
        :value="newsource"
        @input="newsource = $event.target.value"
        /> -->
        <!-- <input v-model="newsource" placeholder="enter a source" />
        <button
          @click="addSource"
         >
          Add Source
        </button> -->
        <p>Type a new source and hit enter:</p>
        <input v-model="newsource" @keyup.enter="addSource()"/>
        </div>

        <div
          v-if="$store.state.username === freet.author"
          class="actions"
        >
        <!-- <textarea
        class="content"
        :value="todelete"
        @input="todelete = $event.target.value"
        /> -->
        <!-- <input v-model="todelete" placeholder="enter a source" />
        <button
          @click="removeSource"
         >
          Remove Source
        </button> -->
        <p>Type a source to remove, and hit enter:</p>
        <input v-model="todelete" @keyup.enter="removeSource()"/>
        
        </div>
      </header>
      <button
        v-if="!showing"
        @click="showSources"
      >
          Show Sources
      </button>
      <button
          v-if="showing"
          @click="quitSources"
      >
          Unexpand Sources
      </button>
    <section v-if="showing">
      <p class="info">
        <!-- Sources: {{ this.sources }} -->
        <li v-for="item in this.sources">
          {{ item }}
        </li>
      </p>
      </section>
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

  
  <script>
  export default {
    name: 'SourceComponent',
    props: {
      // Data from the stored freet
      freet: {
        type: Object,
        required: true
      }
    },
    data() {
      return {
        showing: false, 
        sources: [],
        // sources: '',
        newsource: '',
        todelete: '',
        alerts: {} // Displays success/error messages encountered during freet modification
      };
    },
    watch: {
      freet : function(value) {
          // Whenever the prop freet changes, then get its sources again
          this.getSources();
        }
    },
    created() {
        this.getSources();
    },
    methods: {
      showSources() {
        this.showing = true; 
      },
      quitSources() {
        this.showing = false; 
      }, 
      isValidUrl(str) {
        let url;
        try {
          url = new URL(str);
        } catch (_) {
          return false;
        }
        return true;
      },

      async getSources() {
         /**
         * Get the sources for a freet.
         */
        const url = `/api/source/sources/${this.freet._id}`;
        try {
          const r = await fetch(url);
          const res = await r.json();
          // const res = await r.text();
          this.sources = res; //['data']; 
          // const res = await r.json();
          // if (!r.ok) {
          //   throw new Error(res.error);
          // }
        } catch (e) {
          this.$set(this.alerts, e, 'error');
          setTimeout(() => this.$delete(this.alerts, e), 3000);
        }
      },
      async addSource() {
         /**
         * Add a source to a freet.
         */
         if (!this.isValidUrl(this.newsource)) {
          const error = 'Error: Entered source should be a valid website url.';
          this.$set(this.alerts, error, 'error'); // Set an alert to be the error text, timeout of 3000 ms
          setTimeout(() => this.$delete(this.alerts, error), 3000);
          return;
         }
         
         const requestOptions = {
              method: 'POST',
              headers: {'Content-Type': 'application/json'},
              credentials: 'same-origin', 
              body: JSON.stringify({ source: this.newsource })
          };
        const url = `/api/source/addsource/${this.freet._id}`;
        try {
          const r = await fetch(url, requestOptions);
          const res = await r.json();
          if (!r.ok) {
            throw new Error(res.error);
          }
          this.getSources();
          this.$set(this.alerts, 'Successfully added your source!', 'success');
          setTimeout(() => this.$delete(this.alerts, 'Successfully added your source!'), 3000);
          // this.$store.commit('alert', {
          //     message: 'Successfully added your source!', status: 'success'
          //   });
        } catch (e) {
          this.$set(this.alerts, e, 'error');
          setTimeout(() => this.$delete(this.alerts, e), 3000);
        }
      },
      async removeSource() {
         /**
         * Remove a source from a freet. 
         */
         if (!this.todelete.length) {
          const error = 'Error: Entered source should not be empty.';
          this.$set(this.alerts, error, 'error'); // Set an alert to be the error text, timeout of 3000 ms
          setTimeout(() => this.$delete(this.alerts, error), 3000);
          return;
         }
         const requestOptions = {
              method: 'PUT',
              headers: {'Content-Type': 'application/json'},
              credentials: 'same-origin', 
              body: JSON.stringify({ source: this.todelete })
          };
        const url = `/api/source/delsource/${this.freet._id}`;
        try {
          const r = await fetch(url, requestOptions);
          const res = await r.json();
          if (!r.ok) {
            throw new Error(res.error);
          }
          this.getSources();
          this.$set(this.alerts, 'Successfully removed a source!', 'success');
          setTimeout(() => this.$delete(this.alerts, 'Successfully removed a source!'), 3000);
          // this.$store.commit('alert', {
          //     message: 'Successfully removed a source!', status: 'success'
          //   });
        } catch (e) {
          this.$set(this.alerts, e, 'error');
          setTimeout(() => this.$delete(this.alerts, e), 3000);
        }
      }
    }
  };
  </script>
  
  <style scoped>
  .sources {
      border: 1px solid #06bee1;
      /* border: 1px solid #111; */
      padding: 20px;
      position: relative;
  }

button {
  color: #fff;
  background-color: #1768ac;
}

  h3 {
    color: #06bee1;
  }

  </style>
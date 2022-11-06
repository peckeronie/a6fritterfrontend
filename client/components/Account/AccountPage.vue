<!-- Page for account settings and management -->
<!-- User should be authenticated in order to see this page -->

<template>
  <main>
    <section>
      <header>
        <h2>Account settings for @{{ $store.state.username }}</h2>
      </header>
      <ChangeUsernameForm />
      <ChangePasswordForm />
    </section>
    <section>
      <header>
        <h2>Account management</h2>
      </header>
      <LogoutForm />
      <DeleteAccountForm />
    </section>
    <section class="follow">
      <header>
        <h2>Your Followers and Following</h2>
      </header>
      <p class="info">
        Followers: {{ this.followers }}
      </p>
      <p class="info">
        Following: {{ this.following }}
      </p>
    </section>
  </main>
</template>

<script>
import ChangeUsernameForm from '@/components/Account/ChangeUsernameForm.vue';
import ChangePasswordForm from '@/components/Account/ChangePasswordForm.vue';
import DeleteAccountForm from '@/components/Account/DeleteAccountForm.vue';
import LogoutForm from '@/components/Account/LogoutForm.vue';

export default {
  name: 'AccountPage',
  components: {
    ChangeUsernameForm,
    ChangePasswordForm,
    DeleteAccountForm,
    LogoutForm
  },
  data() {
    return {
      followers: '',
      following: '',
      alerts: {}
    };
  },
  created() {
        this.getFollowers();
        this.getFollowing();
    },
    methods: {
      async getFollowers() {
         /**
         * Get the followers for a user
         */
        const url = `/api/follow/follows/${this.$store.state.username}`;
        try {
          const r = await fetch(url);
          const res = await r.json();
          this.followers = res['response'] ? res['response'] : res['message']; //['data']; 
          // const res = await r.json();
          // if (!r.ok) {
          //   throw new Error(res.error);
          // }
        } catch (e) {
          this.$set(this.alerts, e, 'error');
          setTimeout(() => this.$delete(this.alerts, e), 3000);
        }
      }, 
      async getFollowing() {
         /**
         * Get accounts being followed by a user
         */
        const url = `/api/follow/following/${this.$store.state.username}`;
        try {
          const r = await fetch(url);
          const res = await r.json();
          this.following = res['response'] ? res['response'] : res['message']; //['data']; 
          // const res = await r.json();
          // if (!r.ok) {
          //   throw new Error(res.error);
          // }
        } catch (e) {
          this.$set(this.alerts, e, 'error');
          setTimeout(() => this.$delete(this.alerts, e), 3000);
        }
      }
    }

};
</script>

<style scoped>
.info {
  border: 1px solid #111;
  padding: 20px;
  position: relative;
}
</style>

<script setup lang="ts">
import { User } from '@/components/User';
import { computed, type PropType } from 'vue';
import ProfileView from './ProfileView.vue';
import Footer from '@/components/Footer.vue';

const props = defineProps({
  users: {
    type: Array<User>,
    required: true,
  },
  userState: {
    type: Object as PropType<{ currentUser: User }>,
    required: true,
  }
});
console.log("homeview", props);
const isLoggedIn = computed(() => {
  return props.userState.currentUser.id != -1;
});
</script>

<template>

    <ProfileView v-if="isLoggedIn" :users="props.users" :user="userState.currentUser" :userState="props.userState"/>
    <div v-else>
      <section class="hero is-primary is-fullheight">
        <div class="hero-body">
          <div class="container">
            <h1 class="title">
              Welcome to the Fitness App!
            </h1>
            <h2 class="subtitle">
              Log in or sign up to continue.
            </h2>
          </div>
        </div>
      </section>
    </div>
  <Footer/>
</template>

<style scoped> 
h1, h2{
  color: white;
}
h1{
  font-size: 3rem;
  font-weight: 700;
  margin-bottom: 1rem;
}
h2{
  font-size: 2rem;
  font-weight: 500;
  margin-bottom: 1rem;
}

</style>


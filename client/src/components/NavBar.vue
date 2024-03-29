<script setup lang="ts">
import { computed, onMounted, ref } from 'vue'

import { User } from '@/components/User'
import ProfilePhotoItem from './ProfilePhotoItem.vue';

const navbar = ref({ burgerIsActive: false, loginDropdownIsActive: false });
const darkMode = ref(localStorage.getItem('darkMode') === 'true');

//On component mount, apply the dark mode class if needed
onMounted(() => {
  if (darkMode.value) {
    document.documentElement.classList.add('dark-mode');
  }
});

const toggleDarkMode = () => {
  console.log('Toggling dark mode');
  darkMode.value = !darkMode.value;
  if (darkMode.value) {
    document.body.classList.add('dark-mode');
    localStorage.setItem('darkMode', 'true');
  } else {
    document.body.classList.remove('dark-mode');
    localStorage.setItem('darkMode', 'false');
  }
}

const props = defineProps({
  users: {
    type: Array<User>,
    required: true,
  },
  userState: {
    type: Object,
    required: true,
  }
});

const randomHslColor = () => {
  const hue = Math.floor(Math.random() * 360);
  return `hsl(${hue}, 70%, 50%)`;
}

const emit = defineEmits(['logIn', 'logOut']);

const closeNavbar = () => {
  navbar.value.burgerIsActive = false;
  navbar.value.loginDropdownIsActive = false;
}

const logIn = (user: User) => {
  closeNavbar();
  console.log("navbar logIn", user)
  emit('logIn', user);
}

const logOut = () => {
  closeNavbar();
  emit('logOut');
}

const isLoggedIn = computed(() => {
  return props.userState.currentUser.id != -1;
});
const handleTest = () => {
      console.log('Received test event:');

    };


</script>

<template>
  <nav class="navbar is-primary" role="navigation" aria-label="main navigation">
    <div class="container">
      <div class="navbar-brand">
        <RouterLink class="navbar-item" to="/"><img alt="Vue logo" class="logo" src="@/assets/logo.webp" width="84"></RouterLink>
        <RouterLink v-if="isLoggedIn" class="navbar-item" to="/activity">
          <span class="icon">
            <i class="fas fa-running"></i>
          </span>
          <span>My Activity</span>
        </RouterLink>
        <RouterLink class="navbar-item" to="/about">
          <span class="icon">
            <i class="fas fa-chart-line"></i>
          </span>
          <span>Global Statistics</span>
        </RouterLink>
        <RouterLink v-if="isLoggedIn" class="navbar-item" to="/activity/FriendsActivity">
          <span class="icon">
            <i class="fas fa-user-friends"></i>
          </span>
          <span>Friend Activity</span>
        </RouterLink>
        <!-- On navbar-burger click it toggles 'is-active' class on itself and navbar-menu-->
        <a @click="navbar.burgerIsActive = !navbar.burgerIsActive" :class="{ 'is-active': navbar.burgerIsActive }"
          role="button" class="navbar-burger" aria-label="menu" aria-expanded="false" data-target="navbarMidterm">
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
        </a>
      </div>
      <div :class="{ 'is-active': navbar.burgerIsActive }" id="navbarMidterm" class="navbar-menu">
        <div class="navbar-start">

          <RouterLink class="navbar-item" to="/peoplesearch">People Search</RouterLink>
          <!-- Admin Nav -->
          <div v-if="props.userState.currentUser.isAdmin" class="navbar-item has-dropdown is-hoverable">
            <a class="navbar-link">
              Admin
            </a>
            <div class="navbar-dropdown">
              <RouterLink class="navbar-item" to="/adminUsers">
                Users
              </RouterLink>
            </div>

          </div>

        </div>

        <div class="navbar-end">
          <div class="navbar-item">
            <!--<button @click="toggleDarkMode">Toggle Dark Mode</button>-->
            <div class="buttons">
              <RouterLink to="/signup" v-if="userState.currentUser.id == -1" class="button is-primary">
                <strong>Sign up</strong>
              </RouterLink>
              <RouterLink :to="{ name: 'profile', params: { id: userState.currentUser.id } }" v-else class="button is-primary">
                <strong>{{ userState.currentUser.getName() }}</strong>
              </RouterLink>

              <div id="login-dropdown" :class="{ 'is-active': navbar.loginDropdownIsActive }" class="dropdown">
                <div class="dropdown-trigger">
                  <button v-if="userState.currentUser.id == -1"
                    @click="navbar.loginDropdownIsActive = !navbar.loginDropdownIsActive" class="button"
                    aria-haspopup="true" aria-controls="login-dropdown-menu">
                    <span>Log in</span>
                    <span class="icon is-small">
                      <i class="fas fa-angle-down" aria-hidden="true"></i>
                    </span>
                  </button>
                  <button v-else class="button" @click="logOut">
                    <span>Log Out</span>
                  </button>
                </div>
                <div class="dropdown-menu" id="login-dropdown-menu" role="menu">
                  <div v-for="user in users" @click="logIn(user)" class="dropdown-content">
                    <a href="#" class="dropdown-item card">
                      <div class="card-content">
                        <div class="media">
                          <div class="media-left">
                            <figure class="image is-48x48" :style="{ backgroundColor: randomHslColor() }">
                              <!-- if is admin set class .is-admin -->
                              <ProfilePhotoItem :userId="user.id"></ProfilePhotoItem>
                              <i v-if="user.isAdmin" class="is-admin fa-solid fa-lock-open"></i>
                            </figure>

                          </div>
                          <div class="media-content">
                            <p class="title is-6">
                              {{ user.personalData.firstName }} {{ user.personalData.lastName }}
                            </p>
                            <p class="subtitle is-7">@{{ user.personalData.online_handle }}</p>
                          </div>
                        </div>

                      </div>
                      <!--<hr class="dropdown-divider">-->
                    </a>
                  </div>
                </div>


              </div>
            </div>

            <div class="navbar-item">
              <a class="button">
                <span class="icon">
                  <i class="fab fa-twitter"></i>
                </span>
                <span>Tweet</span>

              </a>
            </div>

          </div>
        </div>
      </div>
    </div>
  </nav>
</template>

<style scoped>
/*:root {                       Plans to make a light and dark mode for te navbar
  --background-color: white;
  --text-color: black;
}

:root.dark-mode {
  --background-color: black;
  --text-color: white;
}

body {
  background-color: var(--background-color);
  color: var(--text-color);
}*/

/*Needed this for navbar color */
.is-primary.is-primary {
  background-color: rgb(0, 0, 0);
}
@media (min-width: 1024px) {
  .dropdown-menu{
    left: auto;
    right: 0;
  }
}
.dropdown-content .media-content{
  overflow-x: visible;
}
.dropdown-content{
  box-shadow: unset;
}
.dropdown-content .card {
  box-shadow: 0px 4px 1em -0.125em rgba(10, 10, 10, 0.1), 0 -1px 20px 1px rgba(10, 10, 10, 0.02);
}


.dropdown-menu {
  max-height: 70vh;
  overflow-y: scroll;
}

.dropdown-menu figure img {
  max-height: unset;
  user-select: none;
  opacity: 0.8;
}

.is-admin {
  position: absolute;
  top: 5px;
  left: 5px;
  font-size: smaller;
  text-shadow: 2px 1px 4px #ffffff, -1px -1px 4px #ffffff;
  color: #f13a3a;
}

.dropdown-menu .dropdown-content {
  width: fit-content;
}

.dropdown-content .media-content p {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  width: 10em;
}

.dropdown-menu .card-content {
  padding: 0rem;
}
@media screen and (max-width: 767px) {
    .navbar-item {
        flex-direction:column;
    }
}
.navbar-item .buttons,
.buttons .button {
  margin-bottom: 0rem;
}

.navbar-item img {
    max-height: unset
}
</style>
<template>
  <nav class="navbar navbar-expand-lg navbar-dark dark-green-background">
    <router-link class="navbar-brand d-flex" :to="{ name: 'Home' }">
      <div class="d-flex flex-column align-items-center">
        <img
          alt="logo"
          src="../assets/img/LendMe-logo-light.png"
          height="45"
        />
      </div>
    </router-link>
    <button
      class="navbar-toggler"
      type="button"
      data-toggle="collapse"
      data-target="#navbarText"
      aria-controls="navbarText"
      aria-expanded="false"
      aria-label="Toggle navigation"
    >
      <span class="navbar-toggler-icon" />
    </button>
    <div class="collapse navbar-collapse" id="navbarText">
      <ul class="navbar-nav mr-auto">
        <li class="nav-item">
          <router-link :to="{ name: 'Home' }" class="nav-link pt-3  text-hov texlit">
            Home
          </router-link>
        </li>
        <li class="nav-item">
          <router-link :to="{ name: 'SearchPage'}" class="nav-link pt-3  text-hov  texlit">
            SEARCH
          </router-link>
        </li>
        <li class="nav-item" v-if="user.isAuthenticated">
          <!-- <router-link :to="{ name: 'Profile' }" class="nav-link"> -->
          <router-link :to="{ name: 'Profile', params: {id: account.id} }" class="nav-link pt-3 text-hov texlit">
            PROFILE
          </router-link>
        </li>
        <li class="nav-item" v-if="user.isAuthenticated">
          <router-link :to="{ name: 'Dashboard', params: {id: account.id} }" class="text-hov pt-3 nav-link texlit">
            DASHBOARD
          </router-link>
        </li>
        <li class="nav-item" v-if="user.isAuthenticated">
          <button type="button" class="btn pt-3 nav-link btn-none text-hov  shadow-none texlit" data-toggle="modal" data-target="#itemCreationModal ">
            CREATE LISTING
          </button>
        </li>
        <li class="nav-item">
          <router-link :to="{ name: 'About' }" class="nav-link pt-3 text-hov texlit">
            ABOUT
          </router-link>
        </li>
      </ul>
      <span class="navbar-text">
        <button
          class="btn btn-outline-primary text-uppercase"
          @click="login"
          v-if="!user.isAuthenticated"
        >
          Login
        </button>

        <div class="dropdown" v-else>
          <div
            class="dropdown-toggle"
            @click="state.dropOpen = !state.dropOpen"
          >
            <img
              :src="user.picture"
              alt="user photo"
              height="40"
              class="rounded"
            />
            <span class="mx-3 texlit">{{ user.name }}</span>
          </div>
          <div
            class="dropdown-menu p-0 list-group w-100"
            :class="{ show: state.dropOpen }"
            @click="state.dropOpen = false"
          >
            <router-link :to="{ name: 'Account' }">
              <div class="list-group-item list-group-item-action hoverable">
                Account
              </div>
            </router-link>
            <div
              class="list-group-item list-group-item-action hoverable"
              @click="logout"
            >
              logout
            </div>
          </div>
        </div>
      </span>
    </div>
  </nav>
</template>

<script>
import { AuthService } from '../services/AuthService'
import { AppState } from '../AppState'
import { computed, reactive } from 'vue'
export default {
  name: 'Navbar',
  setup() {
    const state = reactive({
      dropOpen: false
    })
    return {
      state,
      user: computed(() => AppState.user),
      account: computed(() => AppState.account),
      async login() {
        AuthService.loginWithPopup()
      },
      async logout() {
        await AuthService.logout({ returnTo: window.location.origin })
      }
    }
  }
}
</script>

<style scoped>
.dropdown-menu {
  user-select: none;
  display: block;
  transform: scale(0);
  transition: all 0.15s linear;
}
.dropdown-menu.show {
  transform: scale(1);
}
.hoverable {
  cursor: pointer;
}
a:hover {
  text-decoration: none;
  color:aquamarine!important
}
.nav-link{
  text-transform: uppercase;
}
.nav-item .nav-link.router-link-exact-active{
  color: aquamarine!important;
}
.texlit{
  color: antiquewhite!important;;
}

</style>

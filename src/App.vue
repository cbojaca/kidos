<template>
  <div>
    <nav v-if="currentUser" class="navbar navbar-expand navbar-dark bg-dark">
      <div class="navbar-nav">
        <router-link to="/" class="nav-item nav-link">Home</router-link>
        <router-link v-if="isAdmin" to="/admin" class="nav-item nav-link">Admin</router-link>
        <a @click="logout" class="nav-item nav-link">Logout</a>
      </div>
    </nav>
    <div class="jumbotron">
      <div class="container">
        <div class="row">
          <div class="col-sm-6 offset-sm-3">
            <div v-if="alert.message" :class="`alert ${alert.type}`">{{alert.message}}</div>
            <router-view></router-view>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { authenticationService } from "./_services/authentication.service";
import { router } from "./_helpers/router";
import { Role } from "./_helpers/role";
import { mapState, mapActions } from 'vuex'

export default {
  name: "app",
  data() {
    return {
      currentUser: null
    };
  },
  computed: {
    ...mapState({
      alert: state => state.alert
    }),
    isAdmin() {
      return this.currentUser && this.currentUser.role === Role.Admin;
    }
  },
  created() {
    authenticationService.currentUser.subscribe(x => (this.currentUser = x));
  },
  methods: {
    ...mapActions({
      clearAlert: "alert/clear"
    }),
    logout() {
      authenticationService.logout();
      router.push("/login");
    }
  },
  watch: {
    $route(to, from) {
      // clear alert on location change
      this.clearAlert();
    }
  }
};
</script>
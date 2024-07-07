<template>
  <div class="navbar">
    <div class="navbar-logo">
      <router-link to="/" class="navbar-link"><i class="fas fa-leaf"></i></router-link>
    </div>

    <div class="navbar-menu">
      <router-link to="/tickets" class="navbar-link" v-if="$root.isAuthorized()">
        {{ $root.locale.ticketsList }}
      </router-link>
    </div>

    <div class="navbar-right">
      <template v-if="$root.isAuthorized()">
        <router-link to="/profile" class="navbar-link">
          <i class="fas fa-user"></i>
          {{ $root.user.name || $root.locale.profile }}
        </router-link>
        <a class="navbar-link" @click="$root.logout()">
          <i class="fas fa-sign-out-alt"></i>
          {{ $root.locale.exit }}
        </a>
      </template>
      <template v-else>
        <a class="navbar-link" @click="$root.openAuthModal">
          <i class="fas fa-user"></i>
          {{ $root.locale.enter }}
        </a>
      </template>
    </div>
  </div>
</template>

<style scoped>
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 20px;
  background-color: #f8f9fa;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.navbar-logo {
  font-size: 24px;
  font-weight: bold;
}

.navbar-menu {
  display: flex;
  align-items: center;
  margin-right: auto;
}

.navbar-link {
  margin-right: 20px;
  text-decoration: none;
  color: #000;
  cursor: pointer;
}

.navbar-link:hover {
  color: #007bff;
}

.navbar-right {
  display: flex;
  align-items: center;
}
</style>

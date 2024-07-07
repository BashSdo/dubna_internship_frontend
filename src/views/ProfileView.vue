<template>
  <div class="profile-info">
    <div class="profile-up">
      <figure>
        <img
          src="https://s0.rbk.ru/v6_top_pics/media/img/0/61/346832026749610.webp"
          :alt="$root.locale.profileAvatar"
        />
      </figure>
      <p class="user-name">{{ $root.user.name }}</p>
    </div>

    <div class="profile-body">
      <span>{{ $root.locale.role }}: {{ $root.locale.roles[$root.user.role] }}</span>
      <br />
      <span>{{ $root.locale.id }}: {{ $root.user.id }}</span>
    </div>
  </div>
</template>

<style scoped>
.profile-info {
  margin: 0 auto;
  max-width: 600px;

  background-color: #f3f3f3;
}

.profile-up {
  align-items: center;
  margin-bottom: 20px;

  background-color: #f9f9f9;
  padding: 10px;
}

.profile-body {
  padding: 10px;
}

.profile-up figure {
  width: 100px;
  height: 100px;
  margin-right: 20px;
  overflow: hidden;
  border-radius: 50%;
  margin: 0 auto;
  border: 8px solid #f3f3f3;
}

.profile-up img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: center;
}

.profile-up .user-name {
  font-size: 24px;
  font-weight: bold;
  color: #333;
}
</style>

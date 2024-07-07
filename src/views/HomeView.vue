<template>
  <div class="home-view">
    <div class="home-view__background">
      <img src="@/assets/background.jpg" alt="Background" />
    </div>
    <div class="home-view__content">
      <h1 class="home-view__title">{{ $root.locale.welcomeTitle }}</h1>
      <p class="home-view__description">
        {{ $root.locale.welcomeDescription }}
      </p>
      <div class="home-view__contacts">
        <p class="home-view__contacts-title">
          {{ $root.locale.contacts }}
        </p>
        <p class="home-view__contacts-item">
          {{ $root.locale.phoneNumberTitle }}: {{ $root.locale.phoneNumberValue }}
        </p>
        <p class="home-view__contacts-item">
          {{ $root.locale.emailTitle }}:
          <a href="mailto:test@test.test" class="home-view__contacts-link">
            {{ $root.locale.emailValue }}
          </a>
        </p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.home-view {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  position: relative;
}

.home-view__background {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: -1;
}

.home-view__background img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.home-view__content {
  max-width: 800px;
  padding: 20px;
  background-color: rgba(255, 255, 255, 0.8);
  border-radius: 10px;
  text-align: center;
}

.home-view__title {
  font-size: 36px;
  margin-bottom: 20px;
}

.home-view__description {
  font-size: 18px;
  margin-bottom: 20px;
}

.home-view__contacts {
  font-size: 18px;
}

.home-view__contacts-title {
  font-weight: bold;
  margin-bottom: 10px;
}

.home-view__contacts-item {
  margin-bottom: 5px;
}

.home-view__contacts-link {
  color: #000;
}
</style>

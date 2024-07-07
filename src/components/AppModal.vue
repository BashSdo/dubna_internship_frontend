<template>
  <transition name="modal">
    <div class="modal" v-if="visible">
      <div class="modal-body">
        <div class="modal-header">
          <h3 class="modal-title">{{ title }}</h3>
          <i class="modal-close fas fa-close" @click="$emit('close')"></i>
        </div>

        <div class="modal-content">
          <slot></slot>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
export default {
  name: "AppModal",
  props: {
    title: {
      type: String,
      required: true,
    },
    visible: {
      type: Boolean,
      required: true,
    },
  },
  emits: ["close"],
};
</script>

<style scoped>
.modal-enter-active,
.modal-leave-active {
  transition: opacity 0.3s;
}
.modal-enter,
.modal-leave-to {
  opacity: 0;
}

@keyframes modal-appear {
  from {
    transform: scale(0);
  }

  70% {
    transform: scale(1.05);
  }
  to {
    transform: scale(1);
  }
}
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999;
  background-color: rgba(0, 0, 0, 0.5);
}

.modal-body {
  background-color: white;
  padding: 20px;
  border-radius: 5px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
  width: 400px;
  animation: modal-appear 0.3s;
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
  position: relative;
}

.modal-title {
  font-size: 20px;
  font-weight: bold;
}

.modal-close {
  cursor: pointer;
  font-size: 20px;
  color: #333;
  position: absolute;
  top: 10px;
  right: 10px;
}

.modal-content {
  font-size: 16px;
}
</style>

<template>
  <div>
    <button @click="startTimer" :disabled="isRunning" class="text-3xl">
      ⏱️ {{ formattedTime }}
    </button>
  </div>
</template>

<script>
import { ref, computed } from "vue";

export default {
  setup() {
    const TIME_LEFT = 60 * 2;
    const timeLeft = ref(TIME_LEFT);
    const isRunning = ref(false);
    let timerInterval = null;

    const formattedTime = computed(() => {
      const minutes = Math.floor(timeLeft.value / 60)
        .toString()
        .padStart(2, "0");
      const seconds = (timeLeft.value % 60).toString().padStart(2, "0");
      return `${minutes}:${seconds}`;
    });

    const startTimer = () => {
      if (isRunning.value) return;
      isRunning.value = true;
      timerInterval = setInterval(() => {
        if (timeLeft.value > 0) {
          timeLeft.value--;
        } else {
          clearInterval(timerInterval);
          isRunning.value = false;
        }
      }, 1000);
    };

    return {
      formattedTime,
      startTimer,
      isRunning,
    };
  },
};
</script>

<style scoped>
button {
  padding: 10px 20px;
  cursor: pointer;
}
</style>

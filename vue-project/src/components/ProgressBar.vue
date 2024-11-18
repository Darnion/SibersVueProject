<template>
    <div class="progress-bar">
        <div
            class="progress"
            :style="{ width: progressWidth }"
        ></div>
    </div>
</template>

<script>

export default {
    props: {
        currentStep: {
            type: Number,
            default: 1
        }
    },
    computed: {
        progressWidth() {
        return `${(this.currentStep - 1) * (100 / 4)}%`;
        }
    }
}
</script>

<style>
.progress-bar {
  width: 100%;
  background-color: #e0e0e0;
  border-radius: 5px;
  margin-bottom: 20px;
}

.progress {
  height: 10px;
  background-color: #4caf50;
  border-radius: 5px;
}
</style>
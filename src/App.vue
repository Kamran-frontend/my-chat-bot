<template>
  <div id="app">
    <h1>AI Chatbot</h1>
    <AiChatBot />
  </div>
</template>

<script>
import AiChatBot from "./components/AiChatBot.vue";

export default {
  components: {
    AiChatBot,
  },
};
</script>

<style>
#app {
  text-align: center;
  padding: 20px;
}
</style>

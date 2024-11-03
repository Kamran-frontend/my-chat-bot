<template>
  <div class="chat-bot">
    <div class="chat-header">
      Chatter box!
    </div>
    <div class="chat-window">
      <div class="messages">
        <div v-for="(msg, index) in messages" :key="index" :class="msg.type">
          <span>{{ msg.text }}</span>
        </div>
      </div>
      <input
        v-model="userInput"
        @keypress.enter="sendMessage"
        placeholder="Hello.. I'm listening! Go on.."
      />
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      userInput: "",
      messages: [],
    };
  },
  methods: {
    async sendMessage() {
      if (!this.userInput.trim()) return;

      // Add user message
      this.messages.push({ text: this.userInput, type: "user" });

      // Send request to OpenAI API
      try {
        const response = await axios.post(
          "https://api.openai.com/v1/chat/completions",
          {
            model: "gpt-3.5-turbo",
            stream: true,
            messages: [{ role: "user", content: this.userInput }],
          },
          {
            headers: {
              Authorization: `Bearer ${process.env.VUE_APP_OPENAI_API_KEY}`,
              "Content-Type": "application/json",
            }
          }
        );

        // Add AI response
        this.messages.push({ text: response.data.choices[0].message.content, type: "ai" });
      } catch (error) {
        console.error("Error fetching AI response:", error);
        this.messages.push({ text: "Sorry, something went wrong.", type: "ai" });
      } finally {
        this.userInput = "";
      }
    }
  }
};
</script>

<style scoped>
.chat-bot {
  max-width: 400px;
  margin: auto;
  border: 1px solid #ccc;
  border-radius: 5px;
  overflow: hidden;
  background-color: #f0f0f0;
  font-family: Arial, sans-serif;
}

.chat-header {
  background-color: black;
  color: white;
  font-size: 1.5rem;
  padding: 10px;
  text-align: center;
}

.chat-window {
  display: flex;
  flex-direction: column;
  height: 500px;
}

.messages {
  flex: 1;
  padding: 10px;
  overflow-y: auto;
}

.user {
  text-align: right;
  color: blue;
  background-color: #e0e0ff;
  padding: 10px;
  border-radius: 15px;
  margin-bottom: 10px;
  max-width: 70%;
  margin-left: auto;
}

.ai {
  text-align: left;
  color: green;
  background-color: #d0f0d0;
  padding: 10px;
  border-radius: 15px;
  margin-bottom: 10px;
  max-width: 70%;
  margin-right: auto;
}

input {
  border: 1px solid #ccc;
  padding: 10px;
  width: 100%;
  box-sizing: border-box;
  border-radius: 5px;
  outline: none;
}
</style>

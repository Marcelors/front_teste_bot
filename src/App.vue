<template>
  <div id="app" style="margin: 10px">
    <el-tabs type="border-card">
      <el-tab-pane label="Bot">
        <bot :token="token" v-on:createBot="setBotId"></bot
      ></el-tab-pane>
      <el-tab-pane label="Chat"
        ><chat :botId="botId" :token="token"></chat
      ></el-tab-pane>
      <el-tab-pane label="Chat-SDK"
        ><chat-sdk :botId="botId"></chat-sdk
      ></el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
import Bot from "./views/bot/form";
import Chat from "./views/chat";
import ChatSdk from "./views/chat_skd/Index";

export default {
  name: "App",
  components: {
    Bot,
    Chat,
    ChatSdk,
  },
  data() {
    return {
      botId: null,
      token:
        "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiIxZTU5YzMxMC03YjcxLTQ5MWMtOTk4MC1jMWU3MjNlNGVmZGYiLCJ1ZHQiOiJleUpKWkNJNk1Td2lWR1Z1WVc1MFNXUWlPakVzSWxCbGNuTnZibFI1Y0dVaU9qRXNJbEJ5YjJacGJHVlVlWEJsSWpvekxDSlZjMlZ5VG1GdFpTSTZJbVZrZFNJc0lrUnBjM0JzWVhsT1lXMWxJam9pUldSMUlFMWhiR3h0WVc1dUlpd2lRV05qWlhOelVISnZabWxzWlVsa0lqb3hMQ0pJWVhOQlkyTmxjM05RY205bWFXeGxRV05qWlhOelVuVnNaWE1pT21aaGJITmxMQ0pVYVcxbFdtOXVaVWxrSWpwdWRXeHNmUT09IiwiY3RyIjoicHQtQlIiLCJoc3QiOiJsb2NhbGhvc3QiLCJ0emkiOiJBbWVyaWNhL1Nhb19QYXVsbyIsImljciI6IkZhbHNlIiwiY3d0IjoiIiwibmJmIjoxNjE0NzEwMjE4LCJleHAiOjE2MTQ3MjQ2MTgsImlhdCI6MTYxNDcxMDIxOCwiaXNzIjoiTW92aWRlc2siLCJhdWQiOiJtb3ZpZGVzayJ9.yG5jHmSFkYNVdn5loRRqbXRPzl7TRaLEldXFj_6-3mY",
    };
  },
  methods: {
    setBotId(value) {
      this.botId = value;
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

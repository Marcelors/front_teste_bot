<template>
  <div>
    <div v-if="botId">
      <el-button
        type="primary"
        @click="createConversation"
        v-if="!botInitialized"
        >Iniciar conversa</el-button
      >

      <div v-else v-for="(message, indexPai) in chat" :key="indexPai">
        <p v-for="(text, index) in message.text" :key="`p${index}`">
          {{ text }}
        </p>
        <p v-if="message.uri">
          <a v-if="message.uri" :href="message.uri"> {{ message.uri }} </a>
        </p>

        <el-button
          v-for="(option, index) in message.options"
          :disabled="option.disable"
          :key="`b${index}`"
          type="primary"
          @click="sendMessage(option.id, indexPai)"
        >
          {{ option.text }}
        </el-button>
      </div>
    </div>
    <div v-else>Crie um bot primeiro</div>
  </div>
</template>

<script>
import conversation from "./conversation.json";
import axios from "axios";

export default {
  props: {
    token: {
      type: String,
      required: true,
    },
    botId: {
      type: String,
    },
  },
  data() {
    return {
      flow: conversation,
      chat: [],
      botInitialized: false,
    };
  },
  methods: {
    createConversation() {
      axios
        .patch(`http://localhost:3978/api/bots/${this.botId}/flow`, this.flow, {
          headers: {
            Authorization: "Bearer " + this.token,
          },
        })
        .then((response) => {
          console.log(response);
          this.enableBot();
        })
        .catch((err) => {
          debugger;
          this.$alert(err.response.data.errors.join("; "), "Erro", {
            confirmButtonText: "OK",
          });
        });
    },
    enableBot() {
      axios
        .patch(
          `http://localhost:3978/api/bots/${this.botId}/activation`,
          {
            active: true,
          },
          {
            headers: {
              Authorization: "Bearer " + this.token,
            },
          }
        )
        .then((response) => {
          console.log(response);
          this.$alert("Cadastrado com sucesso", "Info", {
            confirmButtonText: "OK",
          });
          //this.startConversation();
        })
        .catch((err) => {
          debugger;
          this.$alert(err.response.data.errors.join("; "), "Erro", {
            confirmButtonText: "OK",
          });
        });
    },
    startConversation() {
      axios
        .post(`http://localhost:3978/api/messages/initialize`)
        .then((response) => {
          console.log(response);
          this.chat.push(response.data);
          this.botInitialized = true;
        })
        .catch((err) => {
          this.$alert(err.response.data.errors.join("; "), "Erro", {
            confirmButtonText: "OK",
          });
        });
    },
    sendMessage(id, index) {
      for (let i = 0; i < this.chat[index].options.length; i++) {
        const obj = Object.assign({}, this.chat[index].options[i], {
          disable: true,
        });
        this.chat[index].options[i] = obj;
      }

      axios
        .post(`http://localhost:3978/api/messages`, {
          optionId: id,
        })
        .then((response) => {
          console.log(response);
          this.chat.push(response.data);
        })
        .catch((err) => {
          debugger;
          this.$alert(err.response.data.errors.join("; "), "Erro", {
            confirmButtonText: "OK",
          });
        });
    },
  },
};
</script>
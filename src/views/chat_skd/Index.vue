<template>
  <div>
    <div v-if="botId">
      <el-button
        type="primary"
        @click="startConversation"
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
          @click="sendMessage(option.id, option.text, indexPai)"
        >
          {{ option.text }}
        </el-button>
      </div>
    </div>
    <div v-else>Crie um bot primeiro</div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  props: {
    botId: {
      type: String,
    },
  },
  data() {
    return {
      chat: [],
      botInitialized: false,
      conversationId: null,
      serviceUrl: "http://localhost:5000/",
    };
  },
  created() {
    this.$socket.start({
      log: false, // Active only in development for debugging.
    });
  },
  methods: {
    startConversation() {
      var request = {
        channelId: this.botId,
        conversation: {
          id: this.conversationId,
        },
        from: {
          id: this.conversationId,
        },
        membersAdded: [
          {
            id: this.botId,
            name: "Bot",
          },
          {
            id: this.conversationId,
            name: "Client",
          },
        ],
        membersRemoved: [],
        recipient: {
          id: this.botId,
        },
        serviceUrl: this.serviceUrl,
        type: "conversationUpdate",
      };

      axios
        .post(`http://localhost:3978/api/messages`, request)
        .then(() => {
          this.botInitialized = true;
        })
        .catch((err) => {
          this.$alert(err.response.data.errors.join("; "), "Erro", {
            confirmButtonText: "OK",
          });
        });
    },
    sendMessage(id, text, index) {
      for (let i = 0; i < this.chat[index].options.length; i++) {
        const obj = Object.assign({}, this.chat[index].options[i], {
          disable: true,
        });
        this.chat[index].options[i] = obj;
      }

      var request = {
        text: text,
        value: id,
        channelId: this.botId,
        conversation: {
          id: this.conversationId,
        },
        from: {
          id: this.conversationId,
        },
        recipient: {
          id: this.botId,
        },
        serviceUrl: this.serviceUrl,
        type: "message",
      };

      axios
        .post(`http://localhost:3978/api/messages`, request)
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
  sockets: {
    ReceiveMessage(data) {
      console.log(data);

      this.chat.push({
        text: data.message != null ? [data.message] : [],
        options:
          data?.suggestoes?.actions.map((x) => {
            return {
              text: x.title,
              id: x.value,
            };
          }) ?? [],
        uri: data.uri,
      });
    },
    Connection(data) {
      this.conversationId = data;
    },
  },
};
</script>
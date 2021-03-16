<template>
  <el-form ref="form" :model="botForm">
    <el-form-item label="Descrição">
      <el-input v-model="botForm.description"></el-input>
    </el-form-item>
    <el-form-item label="Nome do bot">
      <el-input v-model="botForm.displayName"></el-input>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="add">Save</el-button>
      <el-button>Cancel</el-button>
    </el-form-item>
  </el-form>
</template>

<script>
import axios from "axios";

export default {
  props: {
    token: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      botForm: {
        displayName: "",
        description: "",
      },
    };
  },
  methods: {
    add() {
      axios
        .post("http://localhost:3978/api/bots", this.botForm, {
          headers: {
            Authorization: "Bearer " + this.token,
          },
        })
        .then((response) => {
          console.log(response);
          this.$emit("createBot", response.data.id)
           this.$alert("Cadastrado com sucesso", "Info", {
            confirmButtonText: "OK",
          });
        })
        .catch((err) => {
          this.$alert(err.response.data.errors.join('; '), "Erro", {
            confirmButtonText: "OK",
          });
        });
    },
  },
};
</script>

<style>
</style>
<template>
  <div id="app">
    <h1>Witaj w systemie do zapisów na zajęcia</h1>

    <div v-if="authenticatedUsername">
      <UserPanel :username="authenticatedUsername" @logout="logMeOut()"></UserPanel>
      <MeetingsPage :username="authenticatedUsername"></MeetingsPage>
    </div>

    <div v-else>
      <button :class="signigUp ? 'button-outline' : '' " @click="signigUp=false">Logowanie</button>
      <button :class="!signigUp ? 'button-outline' :  '' " @click="signigUp=true">>Rejestracja</button>

      <div v-if="message" class="alert">
        {{message}}
      </div>

      <LoginForm v-if="!signigUp" @login="(user) => logMeIn(user)"></LoginForm>
      <LoginForm v-else @login="(user) => register(user)" button-label="Załóż konto"></LoginForm>
    </div>
  </div>
</template>

<script>
import "milligram";
import LoginForm from "./LoginForm";
import UserPanel from "./UserPanel";
import MeetingsPage from "./meetings/MeetingsPage";
import axios from "axios";

export default {
  components: {LoginForm, MeetingsPage, UserPanel},
  data() {
    return {
      message: '',
      signigUp: false,
      authenticatedUsername: '',
    }
  },
  methods: {
    logMeIn(user) {
      this.authenticatedUsername = user.login;
    },
    logMeOut() {
      this.authenticatedUsername = '';
    },
    register(user) {
      axios.post('api/participants', user)
          .then(response => {
            // udało się
            this.message = 'Udało się założyć konto'

          })
          .catch(response => {
            // nie udało sie
            this.message = 'Nie udało się założyć konta'
          });
    }

  }
}
</script>

<style>
#app {
  max-width: 1000px;
  margin: 0 auto;
}

.alert {
  max-width: 300px;
  margin: 20px;
  padding: 20px;
  color: green;
  font-family: Arial;
  border: 10px solid green;
}
</style>

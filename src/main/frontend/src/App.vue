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

      <div v-if="message1" class="alert1">
        {{message1}}

      </div>

      <div v-else-if="message2" class="alert2">
        {{message2}}
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
      message1: '',
      message2: '',
      signigUp: false,
      authenticatedUsername: '',
    }
  },
  methods: {
    logMeIn(user) {
      axios.post('api/tokens', user)
          .then(response => {
            this.authenticatedUsername = user.login;
            const token = response.data.token;
            axios.defaults.headers.common['Authorization'] = 'Bearer ' + token;
            axios.get('api/meetings')
                .then(response=> console.log(response.data));
          })
          .catch(response=> {
            this.message2='logowanie nieudane';
          });

    },
    logMeOut() {
      this.authenticatedUsername = '';
      delete axios.defaults.headers.common.Authorization;
    },


    register(user) {
      axios.post('api/participants', user)
          .then(response => {
            // udało się
            this.message1 = 'Udało się założyć konto'

          })
          .catch(response => {
            // nie udało sie
            this.message2 = 'Nie udało się założyć konta'
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

.alert1 {
  max-width: 300px;
  margin: 20px;
  padding: 20px;
  color: green;
  font-family: Arial;
  border: 10px solid green;
}

.alert2 {
  max-width: 300px;
  margin: 20px;
  padding: 20px;
  color: darkred;
  font-family: Arial;
  border: 10px solid darkred;
}
</style>

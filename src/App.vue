<template>
  <v-app>
  <v-card 
    dark
    tiled
    elevation="5"
    class="mx-auto my-15"
    min-width="500px"
    min-height="500px"
  >
    <v-card-title
    class="justify-center my-10 display-3"
    >
      Simple Leaderboard
    </v-card-title>
    <v-card-subtitle
      class="justify-center"
    >
      Enter your email and game name below to get your unique GameID. Remember to write it down!
    </v-card-subtitle>
    <form>
      <v-text-field
        dark
        label="Email"
        :error-messages="emailErrors"
        v-model="email"
        class="text-field mx-auto"
        required
        @input="$v.email.$touch()"
        @blur="$v.email.$touch()"
        >
      </v-text-field>
      <v-text-field
        dark
        label="Game Name"
        :error-messages="gameNameErrors"
        v-model="gameName"
        class="text-field mx-auto"
        required
        :counter="55"
        @input="$v.gameName.$touch()"
        @blur="$v.gameName.$touch()"
        >
      </v-text-field>
      <v-btn 
        dark
        outlined
        @click="submit()" 
        :style="{left:'50%', transform:'translateX(-50%)'}"
      >
        Submit
      </v-btn>
    </form>
    <p
      v-if="gameID !== 0"
      class="text-center my-15"
    >
    Your GameID is {{ gameID }}
    </p>
  </v-card>
  </v-app>
</template>

<script>
import Vue from 'vue'
import axios from 'axios'
import VueAxios from 'vue-axios'
import { validationMixin } from 'vuelidate'
import { required, maxLength, email } from 'vuelidate/lib/validators'

Vue.use(VueAxios, axios)
export default{
  name: "App",
  mixins: [validationMixin],
  validations: {
    email: { required, email },
    gameName: { required, maxLength: maxLength(55) }
  },
  data() {
    return {
      email: "",
      gameName: "",
      gameID: 0
    }
  },
  computed: {
    gameNameErrors () {
        const errors = []
        if (!this.$v.gameName.$dirty) return errors
        !this.$v.gameName.maxLength && errors.push('Game Name cannot be more than 55 characters')
        !this.$v.gameName.required && errors.push('Game Name is required.')
        return errors
      },
    emailErrors () {
      const errors = []
      if (!this.$v.email.$dirty) return errors
      !this.$v.email.email && errors.push('Must be valid email')
      !this.$v.email.required && errors.push('Email is required')
      return errors
    }
  },
  methods: {
  async submit() {
    this.$v.$touch();
    if (this.$v.$invalid) return;
    const info = {action: "addUser",email: this.email, gameName: this.gameName};
    await Vue.axios.post("https://indiealchemy.com/apis/simple-leaderboard/", info).then((response) => {
    this.gameID = response.data
    });
    this.email = "";
    this.gameName = "";
    this.$v.$reset();
  }
}
}
</script>

<style>
#app {
  background: url('assets/parallax-image.jpg') no-repeat center -1500px fixed !important;
  background-size: contain;
}
.text-field {
  width: 400px;
}
</style>

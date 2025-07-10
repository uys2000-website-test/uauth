<template>
  <div>
    <div>
      <select v-model="env">
        <option value="local">local</option>
        <option value="test">test</option>
      </select>
      <button @click="init">init</button>
    </div>
    <div>

      <input type="text" v-model="name" placeholder="name">
      <input type="text" v-model="email" placeholder="name">
      <input type="text" v-model="password" placeholder="name">
      <select v-model="platform">
        <option value="web">web</option>
        <option value="android">android</option>
        <option value="ios">ios</option>
      </select>
      <button @click="signin">sign in</button>
      <button @click="signup">sign up</button>
      <button @click="signout">sign out</button>
      <button @click="signoff">sign off</button>
    </div>
  </div>
</template>

<script lang="ts">
import { UAuth, UHost } from "u-auth"
export default {
  data() {
    return {
      auth: new UAuth(new UHost("local")),
      env: "local" as "local" | "test",
      name: "test",
      email: "test@test.com",
      password: "testpass",
      platform: "web" as "web" | "android" | "ios"
    }
  },
  methods: {
    init() {
      this.auth = new UAuth(new UHost(this.env))
    },
    signin() {
      this.auth.signIn(this.email, this.password, this.platform).then(token => {
        alert(token)
      })
    },
    signup() {
      this.auth.signUp(this.name, this.email, this.password, this.platform).then(token => {
        document.cookie = `token=${token}; path=/; SameSite=Lax`;
        alert(token)
      })
    },
    signout() {
      this.auth.signOut().then((res) => alert(JSON.stringify(res)))
    },
    signoff() {
      this.auth.signOff(this.email, this.password, this.platform).then((res) => alert(JSON.stringify(res)))
    }
  },
  mounted() {
  }
}
</script>

<style scoped>
div {
  display: flex;
  flex-direction: column;
  padding: 4px;
}
</style>

<template>
  <div>
    <div>
      <div style="text-align:  center; color: red;">check console and network for more info</div>
      <span>Init Services</span>
      <select v-model="env">
        <option value="local">local</option>
        <option value="test">test</option>
      </select>
      <button @click="init">init</button>
    </div>
    <div>
      <span>Auth Service</span>
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
    <div>
      <span>Session Service</span>
      <button @click="check">Check Session</button>
      <button @click="list">List Session</button>
      <div>
        <table>
          <thead>
            <tr>
              <th>token</th>
              <th>ip</th>
              <th>platform</th>
              <th>timestamp</th>
              <th></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="session, index in sessions" :key="index">
              <td>{{ session.token }}</td>
              <td>{{ session.ip }}</td>
              <td>{{ session.platform }}</td>
              <td>{{ session.timestamp }}</td>
              <td style="text-align: center;">
                <button @click="() => remove(session.token)">remove</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <div class="info">
      {{ info }}
    </div>
  </div>
</template>

<script lang="ts">
import { UAuthService, UHost, USessionService, type USession } from "u-auth"
export default {
  data() {
    return {
      authService: new UAuthService(new UHost("local", "auth")),
      sessionService: new USessionService(new UHost("local", "session")),
      env: "local" as "local" | "test",
      name: "test",
      email: "test@test.com",
      password: "testpass",
      platform: "web" as "web" | "android" | "ios",
      sessions: [] as USession[],
      info: ""
    }
  },
  methods: {
    init() {
      this.authService = new UAuthService(new UHost(this.env, "auth"))
      this.sessionService = new USessionService(new UHost(this.env, "session"))
    },
    async signin() {
      const token = await this.authService.signIn(this.email, this.password, this.platform)
      this.sessionService.token = token;
      this.info = `signed in, token :${token}`;
    },
    async signup() {
      const token = await this.authService.signUp(this.name, this.email, this.password, this.platform)
      this.sessionService.token = token;
      this.info = `signed up, token :${token}`;
    },
    async signout() {
      const result = await this.authService.signOut()
      this.sessionService.token = "";
      this.info = `signed out, reusult: ${result}`;
    },
    async signoff() {
      const result = await this.authService.signOff(this.email, this.password, this.platform)
      this.sessionService.token = "";
      this.info = `signed off, reusult: ${result}`;
    },
    async check() {
      const result = await this.sessionService.check()
      this.info = `session checked, reusult: ${result}`;
    },
    async list() {
      const result = await this.sessionService.list()
      this.sessions = result;
      this.info = `session listed, session count: ${result.length}`;
    },
    async remove(token: string) {
      const result = await this.sessionService.remove(token)
      this.info = `session removed REFRESH LIST, reusult: ${result}`;
      if (token == this.authService.token) this.authService.token = ""
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

div:has(>table) {
  max-width: 100%;
  overflow: auto;
}

table,
th,
td {
  border: 1px solid black;
  border-collapse: collapse;
}

.info {
  position: fixed;
  bottom: 0;
}
</style>

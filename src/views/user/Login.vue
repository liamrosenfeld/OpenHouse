<template>
  <div id="login">
    <div class="inside">
      <h1>Login</h1>
      <form
        action="#"
        @submit.prevent="submit"
      >
        <div class="group">
          <label for="email">
            Email
          </label>

          <div>
            <input
              v-model="form.email"
              type="email"
              class="form-control"
              name="email"
              autocomplete="email"
              placeholder="Email"
              required
              autofocus
            >
          </div>
        </div>

        <div class="group">
          <label for="password">
            Password
          </label>

          <div>
            <input
              v-model="form.password"
              type="password"
              class="form-control"
              name="current-password"
              autocomplete="current-password"
              placeholder="Password"
              required
            >
          </div>
        </div>

        <div>
          <button
            type="submit"
            class="submit"
          >
            <p>Login</p>
          </button>
        </div>
      </form>

      <div id="toNew">
        <router-link to="/register">
          <p>New here?</p>
        </router-link>
        <router-link to="/forgot">
          <p>Forgot your password?</p>
        </router-link>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";

@Component
export default class Login extends Vue {
  form = {
    email: "",
    password: "",
  };

  submit() {
    this.$store
      .dispatch("user/signIn", {
        email: this.form.email,
        password: this.form.password,
      })
      .then(() => {
        this.$router.push("/");
      })
      .catch(() => {
        this.$toaster.error("Incorrect Username/Password");
      });
  }
}
</script>

<style scoped lang="scss">
@import "@/shared-style/variables.scss";
@import "@/shared-style/mixins.scss";

#login {
  @include shadow-box;
  @include form;
  @include std-size;
  @include std-position;

  #toNew {
    margin-top: 0.5em;
  }
}
</style>

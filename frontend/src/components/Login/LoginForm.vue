<template>
  <v-card width="500px">
    <v-divider></v-divider>
    <v-app-bar dark color="primary" class="mt-n1 mb-2">
      <v-icon large left v-if="!loading">
        {{ $globals.icons.user }}
      </v-icon>
      <v-progress-circular v-else indeterminate color="white" large class="mr-2"> </v-progress-circular>
      <v-toolbar-title class="headline">{{ $t("user.login") }}</v-toolbar-title>
      <v-spacer></v-spacer>
    </v-app-bar>

    <v-form @submit.prevent="login">
      <v-card-text>
        <v-text-field
          v-model="user.email"
          :prepend-icon="$globals.icons.email"
          validate-on-blur
          autocomplete
          autofocus
          :label="`${$t('user.email')} or ${$t('user.username')} `"
          type="email"
        ></v-text-field>
        <v-text-field
          v-model="user.password"
          class="mb-2s"
          autocomplete
          :prepend-icon="$globals.icons.lock"
          :label="$t('user.password')"
          :type="showPassword ? 'text' : 'password'"
          :append-icon="showPassword ? $globals.icons.eye : $globals.icons.eyeOff"
          @click:append="showPassword = !showPassword"
        ></v-text-field>
        <v-card-actions>
          <v-btn v-if="options.isLoggingIn" color="primary" block large type="submit">{{ $t("user.sign-in") }} </v-btn>
        </v-card-actions>

        <v-alert v-if="error" class="mt-3 mb-0" type="error">
          {{ $t("user.could-not-validate-credentials") }}
        </v-alert>
      </v-card-text>
    </v-form>
  </v-card>
</template>

<script>
import { api } from "@/api";
export default {
  props: {},
  data() {
    return {
      loading: false,
      error: false,
      showLogin: false,
      showPassword: false,
      user: {
        email: "",
        password: "",
      },
      options: {
        isLoggingIn: true,
      },
    };
  },
  mounted() {
    this.clear();
  },
  methods: {
    clear() {
      this.user = { email: "", password: "" };
    },
    async login() {
      this.loading = true;
      this.error = false;
      let formData = new FormData();
      formData.append("username", this.user.email);
      formData.append("password", this.user.password);
      const response = await api.users.login(formData);
      if (!response) {
        this.error = true;
      } else {
        this.clear();
        this.$store.commit("setToken", response.data.access_token);
        this.$emit("logged-in");
        this.$store.dispatch("requestUserData");
      }

      this.loading = false;
    },
  },
};
</script>

<style></style>

<template>
  <v-container
    id="loginBox"
    :class="{
      'login-box': true,
      'elevation-5': true,
      'login-box-hide': boxHide,
    }"
  >
    <v-form ref="form" v-model="valid" lazy-validation @submit="login">
      <v-text-field
        v-model="username"
        :rules="rules"
        label="Username"
        required
        hide-details
        dark
        @keypress="press"
      ></v-text-field>

      <v-text-field
        v-model="password"
        label="Password"
        :rules="rules"
        required
        type="password"
        hide-details
        dark
        @keypress="press"
      ></v-text-field>
      <div class="d-flex justify-end mt-4">
        <v-btn outlined small class="mr-4" color="error" @click="hideBox">
          Close
        </v-btn>
        <v-btn small class="" color="success" @click="login"> Login </v-btn>
      </div>
    </v-form>
  </v-container>
</template>

<script>
export default {
  data: () => ({
    valid: true,
    username: "",
    rules: [(v) => !!v || "Required"],
    password: "",
    boxHide: true,
  }),
  mounted: function () {},
  methods: {
    login(e) {
      if (e !== undefined) e.preventDefault();
      const valid = this.$refs.form.validate();
      if (valid) {
        this.ax.post(
          this.config.testEnvBackEndUrl + "user/login",
          {
            username: this.username,
            password: this.password,
          },
          {
            success: (response) => {
              const code = response.data.code;
              if (code === 0) {
                const token = response.data.body.token;
                this.successToast("Login succeed!");
                this.$store.commit("login", {
                  token,
                });
                this.hideBox();
              }
              if (code === 1) {
                this.errorToast("Wrong username!");
              }
              if (code === 2) {
                this.errorToast("Wrong password!");
              }
            },
          }
        );
      }
    },
    press(e) {
      if (e.key === "Enter") {
        this.login();
      }
    },
    showBox() {
      this.boxHide = false;
    },
    hideBox() {
      this.boxHide = true;
    },
  },
};
</script>

<style scoped>
.login-box {
  position: absolute;
  background-color: #272f3a;
  right: 1rem;
  z-index: 3;
  top: 4rem;
  padding: 1rem;
  border-radius: 0.7rem;
  width: 300px;
  transition: all 0.6s;
  opacity: 1;
  right: 1rem;
}
.login-box-hide {
  /* top: -16%; */
  right: -33%;
  opacity: 0;
}
</style>

<template>
  <div class="login">
    <div class="container">
      <div class="row">
        <div class="col-lg-6 offset-lg-3 col-sm-10 offset-sm-1">
          <form
            class="text-center border border-primary p-5"
            style="
              margin-top: 70px;
              height: auto;
              padding-top: 100px !important;
            "
            @submit.prevent="loginUser"
          >
            <h2 class="display-4">Sign In</h2>
            <br />
            <br />
            <br />
            <input
              type="text"
              id="email"
              class="form-control mb-5"
              placeholder="Email"
              v-model="login.email"
            />
            <!-- Password -->
            <input
              type="password"
              id="password"
              class="form-control mb-5"
              placeholder="Password"
              v-model="login.password"
            />
            <p>
              Dont have an account?
              <router-link to="/register">click here</router-link>
            </p>
            <!-- Sign in button -->
            <center>
              <button class="btn btn-primary btn-block w-75 my-4" type="submit">
                Sign in
              </button>
            </center>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import swal from "sweetalert";
export default {
  data() {
    return {
      login: {
        email: "",
        password: "",
      },
    };
  },
  methods: {
    async loginUser() {
      if (!this.login.email || !this.login.password) {
        swal("Try Again!", "Fields cannot be blank");
        return;
      }
      try {
        let response = await axios.post("/api/login", this.login);
        let token = response.data.token;
        localStorage.setItem("jwt", token);
        if (token) {
          swal("Success", "Login Successful", "success");
          this.$root.$data.user = response.data.user;
          this.$router.push({
            path: "User",
            params: { id: this.$root.$data.user.username },
          });
        }
      } catch (err) {
        this.$root.$data.user = null;
        if (err == undefined || err.response == undefined) {
          swal("Error", "Failed to connect. Please try again later.", "error");
        } else if (err.response.status == 400) {
          swal("Error", "Invalid Login.", "error");
        } else if (err.response.status == 401) {
          swal(
            "Error",
            "Login Failed. Check Authorization Credentials.",
            "error"
          );
        } else {
          swal("Error", "Register failed.", "error");
          swal("Error", "Failed to connect. Please try again later.", "error");
          console.log(err.response);
        }
      }
    },
  },
};
</script>

<style scoped>
.container {
  margin-bottom: 1em;
}
</style>

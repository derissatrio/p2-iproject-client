<template>
  <div>
    <div class="container mt-3">
      <b-card style="margin-left: 300px; margin-right: 300px">
        <h2 style="font-weight: bold" v-text="page"></h2>
        <b-form @submit.prevent="onClick">
          <b-form-group
            v-if="page === 'Register'"
            class="text-left"
            id="input-group-0"
            label="Name:"
            label-for="input-0"
            ><b-form-input
              v-model="name"
              id="input-0"
              type="text"
              placeholder="Enter Name"
              required
            ></b-form-input>
          </b-form-group>
          <b-form-group
            class="text-left"
            id="input-group-1"
            label="Email:"
            label-for="input-1"
            description="We'll never share your email with anyone else."
            ><b-form-input
              v-model="email"
              id="input-1"
              type="email"
              placeholder="Enter email"
              required
            ></b-form-input>
          </b-form-group>
          <b-form-group
            v-if="page === 'Register'"
            class="text-left"
            id="input-group-0"
            label="No Telp:"
            label-for="input-0"
            ><b-form-input
              v-model="noTelp"
              id="input-0"
              type="text"
              placeholder="Enter Number"
              required
            ></b-form-input>
          </b-form-group>
          <b-form-group
            id="input-group-2"
            label="Password:"
            label-for="input-2"
          >
            <b-form-input
              v-model="password"
              type="password"
              id="input-2"
              placeholder="Enter name"
              required
            ></b-form-input>
          </b-form-group>
          <b-button style="width: 100%" type="submit" variant="primary"
            >Submit</b-button
          >
          <p v-if="page === 'Login'" class="my-3">
            Register not yet?
            <router-link to="/register" style="font-weight: bold"
              >Register
            </router-link>
            <br />
            or Sign in with Google Account <br />
          </p>

          <p v-if="page === 'Register'" class="my-3">
            Have an account?
            <router-link to="/login" style="font-weight: bold"
              >Login
            </router-link>
            <br />
            or Sign in with Google Account <br />
          </p>
          <GoogleLogin
            class="d-flex justify-content-center"
            :params="params"
            :renderParams="renderParams"
            :onSuccess="onSuccess"
          ></GoogleLogin>
        </b-form>
      </b-card>
    </div>
  </div>
</template>

<script>
import GoogleLogin from 'vue-google-login';
import Swal from "sweetalert2"
export default {
  props: [ "page" ],
  name: "Form",
  components: {
    GoogleLogin
  },
  data() {
    return {
      name: "",
      email: "",
      password: "",
      noTelp: "",
      params: {
        client_id:
          "730600242847-6adjtmksme5k71gnlrotkm1opoi3vpe1.apps.googleusercontent.com",
      },
      // only needed if you want to render the button with the google ui
      renderParams: {
        width: 250,
        height: 50,
        longtitle: true,
      },
    }
  },
  methods: {
    onClick() {
      this.$emit("onClick", {
        email: this.email,
        password: this.password,
        name: this.name,
        noTelp: this.noTelp
      })
    },
    onSuccess(googleUser) {
      const id_token = googleUser.getAuthResponse().id_token;
      console.log("🚀 ~ file: Form.vue ~ line 131 ~ onSuccess ~ id_token", id_token)
      this.$store
        .dispatch("oAuth2", id_token)
        .then(({ data }) => {
          localStorage.setItem("access_token", data.accessToken);
          localStorage.setItem("name", data.name);
          localStorage.setItem("role", data.role);
          localStorage.setItem("oAuth2", true);
          this.$router.push({
            name: "Home",
          });
        })
        .catch((err) => {
          console.log(err);
          Swal.fire({
            icon: "error",
            title: err.response.data.message,
          });
        });
    },
  }
}
</script>

<style>

</style>
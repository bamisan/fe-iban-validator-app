<template>
  <v-container class="fill-height">
    <v-row align="center" justify="center">
      <v-col cols="4" sm="4" md="4">
        <v-card :loading="loading" class="pa-1 custom-card">
          <v-card-title class="text-h5 text-center">IBAN WEB APP</v-card-title>
          <v-card-text class="mt-2">
            <v-form>
              <v-text-field
                v-model="credentials.name"
                label="Name"
                :error-messages="errors.name"
                required
                variant="outlined"
                @input="clearError('name')"
              ></v-text-field>

              <v-text-field
                v-model="credentials.email"
                label="Email"
                :error-messages="errors.email"
                required
                type="email"
                variant="outlined"
                class="mt-2"
                @input="clearError('email')"
              ></v-text-field>

              <v-text-field
                v-model="credentials.password"
                label="Password"
                :error-messages="errors.password"
                required
                type="password"
                variant="outlined"
                class="mt-2"
                @input="clearError('password')"
              ></v-text-field>

              <v-text-field
                v-model="credentials.confirmPassword"
                label="Confirm Password"
                :error-messages="errors.confirm_password"
                required
                type="password"
                variant="outlined"
                class="mt-2"
                @input="clearError('confirm_password')"
              ></v-text-field>

              <v-btn class="mt-2" block color="primary" @click="submit">
                Register
              </v-btn>
              <v-btn class="mt-2" block text :to="{ name: 'login' }">
                Already have an account? Login
              </v-btn>
            </v-form>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
import { ref } from "vue";
import axios from "@/services/axios";
import { useRouter } from "vue-router";

const router = useRouter();

const credentials = ref({
  name: "",
  email: "",
  password: "",
  confirmPassword: "",
});
const loading = ref(false);
const errors = ref({});

const clearError = (field) => {
  if (errors.value[field]) {
    delete errors.value[field];
  }
};

const submit = async () => {
  loading.value = true;

  try {
    const { data } = await axios.post("register", {
      name: credentials.value.name,
      email: credentials.value.email,
      password: credentials.value.password,
      confirm_password: credentials.value.confirmPassword,
    });

    localStorage.setItem("token", data.data.token);
    localStorage.setItem("role", "user");

    alert("Registered successfully.");

    router.push({ name: "user-dashboard" });

    errors.value = {};

    loading.value = false;
  } catch (error) {
    if (error.response && error.response.status === 422) {
      errors.value = error.response.data.errors;
    } else {
      console.error("Unexpected error:", error.message);
    }
    loading.value = false;
  }
};
</script>

<style scoped>
.custom-card {
  box-shadow: 0 4px 6px;
}
</style>

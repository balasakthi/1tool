<template>
  <v-lazy>
    <v-row class="align-center justify-center">
      <v-col>
        <v-card
          class="mx-auto pa-12 pb-8"
          elevation="8"
          rounded="lg"
          max-width="430"
        >
          <v-img
            class="mx-auto my-4"
            max-width="128"
            src="/logo.png"
            lazy-src="/logo.png"
          ></v-img>
          <h1
            class="mb-3 text-subtitle-1 font-weight-bold text-primary text-center"
          >
            Wir sind jetzt 1Tool...
          </h1>
          <v-form @submit.prevent="handleLogin" v-model="isValid">
            <div class="text-subtitle-1 text-medium-emphasis">Email:</div>

            <v-text-field
              v-model="email"
              density="compact"
              placeholder="Email address"
              prepend-inner-icon="mdi-email-outline"
              variant="outlined"
              :rules="emailRules"
            ></v-text-field>

            <div
              class="text-subtitle-1 text-medium-emphasis d-flex align-center justify-space-between"
            >
              Password:
            </div>

            <v-text-field
              v-model="password"
              :append-inner-icon="visible ? 'mdi-eye-off' : 'mdi-eye'"
              :type="visible ? 'text' : 'password'"
              density="compact"
              placeholder="Enter your password"
              prepend-inner-icon="mdi-lock-outline"
              variant="outlined"
              @click:append-inner="visible = !visible"
              :rules="passwordRules"
            ></v-text-field>

            <v-row :class="[userStore.error ? 'mt-0' : 'd-none']">
              <v-col>
                <span class="text-red"> {{ userStore.error }}</span>
              </v-col></v-row
            >

            <div
              class="my-3 d-flex flex-row align-center justify-space-between"
            >
              <a
                class="text-decoration-none text-blue"
                href="#"
                rel="noopener noreferrer"
                target="_blank"
              >
                Forgot password?</a
              >
              <v-btn
                :loading="isLoading"
                type="submit"
                class="text-subtitle-1"
                color="primary"
                flat
              >
                Login
              </v-btn>
            </div>
          </v-form>

          <v-card-text class="pb-0 text-center">
            <a
              class="text-blue text-decoration-none"
              href="https://my.1tool.com"
              rel="noopener noreferrer"
              target="_blank"
            >
              1Tool
            </a>
            &copy {{ new Date().getFullYear() }}
          </v-card-text>
        </v-card>
      </v-col>
    </v-row></v-lazy
  >
</template>

<script setup>
import { ref, onMounted } from "vue";
import { useUserStore } from "~/stores/user";

const userStore = useUserStore();

const isLoading = ref(false);
const email = ref("demo@1tool.com");
const password = ref("1234");

const visible = ref(false);
const isValid = ref(false);

definePageMeta({
  layout: "noauth",
  middleware: ["already-auth"],
});

onMounted(() => {
  userStore.setError();
});

const emailRules = [
  (value) => !!value || "Email is required",
  (value) =>
    /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/i.test(value) ||
    "Email must be valid",
];

const passwordRules = [(value) => !!value || "Password is required"];

const handleLogin = async () => {
  if (isValid.value) {
    isLoading.value = true;

    await userStore.signIn({
      email: email.value,
      password: password.value,
    });

    isLoading.value = userStore.error ? false : true;

    if (!userStore.error) await navigateTo("/");
  }
};

useSeoMeta({
  title: "Login - 1Tool Freelancer App",
});
</script>

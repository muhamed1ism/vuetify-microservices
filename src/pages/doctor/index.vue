<script setup>
import { useUserStore } from "@/stores/useUserStore";
import { useDoctorStore } from "@/stores/useDoctorStore";
import { useAuthStore } from "@/stores/useAuthStore";
import router from "@/router";

const authStore = useAuthStore();
const userStore = useUserStore();
const doctorStore = useDoctorStore();
const role = userStore.user.role;

const user = doctorStore.doctor;
const title = "Moji podaci";
const items = [
  {
    title: "Ime",
    value: `${user.firstName}`,
  },
  {
    title: "Prezime",
    value: `${user.lastName}`,
  },
  {
    title: "Specijalnost",
    value: `${user.specialty}`,
  },
  {
    title: "Adresa",
    value: `${user.address}`,
  },
  {
    title: "Broj telefona",
    value: `${user.phoneNumber}`,
  },
  {
    title: "Datum rođenja",
    value: `${user.dateOfBirth}`,
  },
];

if (!authStore.auth.isAuthenticated) {
  router.push("/login");
}

if (!authStore.auth.hasProfile) {
  router.push("/doctor/create");
}
</script>

<template>
  <v-container class="fluid fill-height">
    <v-row class="justify-center align-center mb-16">
      <v-col cols="12" sm="8" md="6" lg="4">
        <v-card border variant="flat" class="pa-4 mx-auto">
          <v-card-title class="text-center text-h5">{{ title }}</v-card-title>
          <v-card-item>
            <v-sheet>
              <v-card-text
                v-for="item in items"
                :key="item"
                class="v-card-text"
              >
                <div class="text-subtitle-1 text-medium-emphasis">
                  {{ item.title }}
                </div>
                <v-text-field
                  disabled
                  readonly
                  density="compact"
                  variant="outlined"
                  >{{ item.value }}</v-text-field
                >
              </v-card-text>
              <v-btn
                border
                block
                size="large"
                class="mb-8 mt-6"
                color="blue-darken-2"
                variant="tonal"
                append-icon="mdi-pencil"
                :to="`/doctor/update`"
                >Uredi</v-btn
              >
            </v-sheet>
          </v-card-item>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<style scoped>
.v-card-text {
  padding: 0 !important;
}
</style>

<script setup>
import { ref } from "vue";
import { useUserStore } from "@/stores/useUserStore";
import { useAuthStore } from "@/stores/useAuthStore";
import { useDoctorStore } from "@/stores/useDoctorStore";
import { useAppointmentStore } from "@/stores/useAppointmentStore";
import router from "@/router";

const form = ref({
  doctorId: "",
  date: null,
  time: "",
});

const userStore = useUserStore();
const authStore = useAuthStore();
const doctorStore = useDoctorStore();
const appointmentStore = useAppointmentStore();
const role = userStore.user.role;
const body = document.querySelector("body");

doctorStore.getDoctors();

const dateFormat = (date) => {
  return date.toLocaleDateString("hr-HR");
};

const convertToISO = (date) => {
  return new Date(date).toISOString();
};

const submit = async () => {
  try {
    form.value.date = convertToISO(form.value.date);
    await appointmentStore.createAppointment(form.value);
  } catch (error) {
    console.log(error);
  }
};

if (!authStore.auth.isAuthenticated) {
  router.push("/login");
}

if (!authStore.auth.hasProfile) {
  router.push("/doctor/create");
}

if (role !== "patient") {
  console.log("Nemate pristup ovoj stranici");
  router.push("/dashboard");
}
</script>

<template>
  <v-container class="fluid fill-height">
    <v-row class="justify-center align-center mb-16">
      <v-col cols="12" sm="8" md="6" lg="4">
        <v-card border variant="flat" class="pa-4 mx-auto">
          <v-card-title class="text-center text-h5"
            >Naruči termin kod doktora</v-card-title
          >
          <v-card-item>
            <v-sheet>
              <v-form @submit.prevent="submit">
                <div class="text-subtitle-1 text-medium-emphasis">
                  Odaberi doktora
                </div>
                <v-select
                  v-model="form.doctorId"
                  :items="doctorStore.doctors"
                  :item-title="(doctor) => doctor.name"
                  :item-value="(doctor) => doctor.id"
                  variant="outlined"
                  density="compact"
                ></v-select>

                <div class="text-subtitle-1 text-medium-emphasis">
                  Datum rođenja
                </div>
                <VueDatePicker
                  class="vue-date-picker mb-6"
                  v-model="form.date"
                  placeholder="Unesi datum termina"
                  :format="dateFormat"
                  locale="hr"
                  auto-apply
                  disable-year-select
                  :enable-time-picker="false"
                  :teleport="body"
                ></VueDatePicker>

                <div class="text-subtitle-1 text-medium-emphasis">
                  Odaberi vrijeme
                </div>
                <VueDatePicker
                  class="mb-8"
                  placeholder="Unesi vrijeme"
                  v-model="form.time"
                  locale="hr"
                  hide-input-icon
                  :teleport="body"
                  time-picker
                ></VueDatePicker>

                <v-btn
                  border
                  block
                  type="submit"
                  variant="tonal"
                  color="blue-darken-2"
                  size="large"
                  class="mb-8 mt-2"
                  >Naruči se</v-btn
                >
              </v-form>
            </v-sheet>
          </v-card-item>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<style scoped></style>

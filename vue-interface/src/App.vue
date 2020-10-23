<template>
  <div id="main-app" class="container">
    <div class="row justify-content-center">
      <appointment-list :appointments="appointments" />
    </div>
  </div>
</template>

<script>
import AppointmentList from "./components/AppointmentList";
import axios from "axios";

export default {
  name: "MainApp",
  components: {
    AppointmentList,
  },
  data: function() {
    return {
      title: "Appointment List",
      appointments: [],
    };
  },

  mounted() {
    axios
      .get("./data/appointments.json")
      .then((res) => (this.appointments = res.data));
  },
};
</script>

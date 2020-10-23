<template>
  <div id="main-app" class="container">
    <div class="row justify-content-center">
      <add-appointment @add="addItem" />
      <search-appointments @searchRecords="SearchAppointments" />
      <appointment-list
        :appointments="filterdApts"
        @remove="removeItem"
        @edit="editItem"
      />
    </div>
  </div>
</template>

<script>
import AddAppointment from "./components/AddAppointment";
import AppointmentList from "./components/AppointmentList";
import SearchAppointments from "./components/SearchAppointments";

import _ from "lodash";
import axios from "axios";

export default {
  name: "MainApp",
  data: function() {
    return {
      appointments: [],
      filterKey: "petName",
      filterDir: "asc",
      searchTerms: "",
      aptIndex: 0,
    };
  },
  components: {
    AddAppointment,
    AppointmentList,
    SearchAppointments,
  },
  mounted() {
    axios.get("./data/appointments.json").then(
      (res) =>
        (this.appointments = res.data.map((item) => {
          item.aptId = this.aptIndex;
          this.aptIndex++;
          return item;
        }))
    );
  },
  computed: {
    searchedApt: function() {
      return this.appointments.filter((item) => {
        return (
          item.petName.toLowerCase().match(this.searchTerms.toLowerCase()) ||
          item.petOwner.toLowerCase().match(this.searchTerms.toLowerCase()) ||
          item.aptNotes.toLowerCase().match(this.searchTerms.toLowerCase())
        );
      });
    },
    filterdApts: function() {
      return _.orderBy(
        this.searchedApt,
        (item) => {
          return item[this.filterKey].toLowerCase();
        },
        this.filterDir
      );
    },
  },
  methods: {
    SearchAppointments: function(terms) {
      this.searchTerms = terms;
    },
    addItem: function(apt) {
      apt.aptId = this.aptIndex;
      this.aptIndex++;

      this.appointments.push(apt);
    },
    removeItem: function(apt) {
      this.appointments = _.without(this.appointments, apt);
    },
    editItem: function(id, field, text) {
      const aptIndex = _.findIndex(this.appointments, {
        aptId: id,
      });
      this.appointments[aptIndex][field] = text;
    },
  },
};
</script>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      movies: [],
      timeslots: {},
      reports: { nestedSlots: [], sales: {} },
      movieTitle: "",
      movieDate: "",
      movieTime: "",
      movieTimeSlot: "",
      theaterNumber: null,
      movieSchedule: [],
    };
  },
  created: function () {
    this.movieIndex();
    this.timeSlotIndex();
    this.displayReports();
  },
  methods: {
    movieIndex: function () {
      axios.get("http://localhost:3000/movies").then((response) => {
        console.log(response.data);
        this.movies = response.data;
      });
    },
    timeSlotIndex: function () {
      axios.get("http://localhost:3000/timeslots").then((response) => {
        console.log(response.data);
        this.timeslots = response.data;
      });
    },
    timeSlotSet: function () {
      this.movieTimeSlot = this.movieTime + " " + this.movieDate;
      var formData = new FormData();
      // var movieID =
      formData.append("movie_id", 1);
      formData.append("time_slot", this.movieTimeSlot);
      formData.append("theater_number", this.theaterNumber);
      axios.post("http://localhost:3000/timeslots", formData).then((response) => {
        console.log(response.data);
      });
    },
    displayReports: function () {
      axios.get("http://localhost:3000/reports").then((response) => {
        console.log(response.data);
        this.reports = response.data;
      });
    },
  },
};
</script>

<template>
  <div>
    <form v-on:submit.prevent="timeSlotSet()">
      <!-- This is going to be made a dropdown that displays movie title and sets input to movie ID -->
      Title:
      <input type="text" v-model="movieTitle" />
      Day:
      <input type="date" v-model="movieDate" />
      Time:
      <input type="time" v-model="movieTime" />
      Theater Number:
      <input type="number" v-model="theaterNumber" />
      <input class="btn btn-primary mt-3" type="Submit" value="Add to Schedule" />
    </form>
  </div>
  <div>
    <h1>Current Schedule:</h1>
    <table class="table table-bordered">
      <thead>
        <tr>
          <th scope="col">Theater</th>
          <th scope="col">Title</th>
          <th scope="col">Time</th>
        </tr>
      </thead>
      <tbody v-for="timeslot in timeslots" v-bind:key="timeslot.id">
        <tr>
          <th scope="row">{{ timeslot.theater_number }}</th>
          <td>{{ timeslot.movie.title }}</td>
          <td>{{ timeslot.time_slot }}</td>
        </tr>
      </tbody>
    </table>
  </div>
  <h1>Reports</h1>
  <div v-for="report in reports" v-bind:key="report.id">
    <h2>{{ report.name }}</h2>
    <h3 v-for="timeslot in reports.nestedSlots" v-bind:key="timeslot.id">{{ timeslot.movie.title }}</h3>
    <p>{{ report.sales }}</p>
  </div>
  <div class="movies" v-for="movie in movies" v-bind:key="movie.id">
    <h1>{{ movie.title }}</h1>
    <img v-bind:src="movie.poster_url" />
    <p>{{ movie.runtime }} minutes</p>
    <p>{{ movie.summary }}</p>
  </div>
</template>

<style>
img {
  max-width: 300px;
}
</style>

<template>
  <div class="home">
    <div class="" style="font-size: 22px; font-weight: 700; margin: 40px 0px">
      Asteriod Neo Feed
    </div>
    <div class="select-date">
      <div class="start-date">
        <label for=""> Select Start Date</label>
        <b-form-datepicker
          id="example-datepicker"
          v-model="startdate"
          class="mb-2"
        ></b-form-datepicker>
      </div>
      <div class="end-date">
        <label for=""> Select End Date</label>

        <b-form-datepicker
          id="example-datepicker2"
          v-model="enddate"
          class="mb-2"
        ></b-form-datepicker>
      </div>
    </div>

    <b-button variant="outline-primary" @click="GetAsteriod()" >Submit</b-button>
    <div v-if="Err && !asteriodData" style="color: red; margin: 10px">{{ Err }}</div>
    <div
      v-if="asteriodData"
      style="display: flex; justify-content: space-evenly; margin-top: 25px"
    >
      <b-button variant="outline-primary" @click="GetFast()"
        >Fastest Asteriod</b-button
      >
      <b-button variant="outline-primary" @click="GetClose()"
        >Closest Asteriod</b-button
      >
      <b-button variant="outline-primary" @click="GetSize()"
        >Average Size Asteriod</b-button
      >
    </div>

    <div v-if="FastAsteriod == true">
      <div style="margin-top: 15px; font-size: 24px; font-weight: 700">
        Fastest Asteroid in km/h
      </div>
      <div
        style="display: flex; justify-content: space-around; margin-top: 25px"
      >
        <div>Asteriod Id</div>
        <div>By Date</div>
        <div>KM/H :</div>
      </div>
      <div v-for="(asteriod, index) in asteriodData" :key="index">
        <div v-for="(asteriod1, index) in asteriod" :key="index">
          <div
            v-for="(asterioddate, index) in asteriod1.close_approach_data"
            :key="index"
          >
            <div style="display: flex; justify-content: space-around">
              <div>
                {{ asteriod1.id }}
              </div>
              <div>
                {{ asterioddate.close_approach_date }}
              </div>
              <div>
                {{ asterioddate.relative_velocity.kilometers_per_hour }} Km/H
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div v-if="CloseAsteriod == true">
      <div style="margin-top: 15px; font-size: 24px; font-weight: 700">
        Closest Asteroid By Distance
      </div>

      <div
        style="display: flex; justify-content: space-around; margin-top: 25px"
      >
        <div>Asteriod Id</div>
        <div>By Date</div>
        <div>Closest Asteroid By Distance</div>
      </div>
      <div v-for="(asteriod, index) in asteriodData" :key="index">
        <div v-for="(asteriod1, index) in asteriod" :key="index">
          <div
            v-for="(asterioddate, index) in asteriod1.close_approach_data"
            :key="index"
          >
            <div style="display: flex; justify-content: space-around">
              <div>
                {{ asteriod1.id }}
              </div>
              <div>
                {{ asterioddate.close_approach_date }}
              </div>
              <div>
                {{
                  asterioddate.miss_distance.miles *
                  asterioddate.relative_velocity.kilometers_per_hour
                }}
                Distance
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div v-if="AvgAsteriod == true">
      <div style="margin-top: 15px; font-size: 24px; font-weight: 700">
        Average Size of the Asteroids in kilometers
      </div>
      <div
        style="display: flex; justify-content: space-around; margin-top: 25px"
      >
        <div>Asteriod Id</div>
        <div>By Date</div>
        <div>Average size Asteriod in KM</div>
      </div>
      <div v-for="(asteriod, index) in asteriodData" :key="index">
        <div v-for="(asteriod1, index) in asteriod" :key="index">
          <div
            v-for="(asterioddate, index) in asteriod1.close_approach_data"
            :key="index"
          >
            <div style="display: flex; justify-content: space-around">
              <div>
                {{ asteriod1.id }}
              </div>
              <div>
                {{ asterioddate.close_approach_date }}
              </div>
              <div>{{ asterioddate.miss_distance.miles * 1.609 }} Km</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from "axios";

export default {
  name: "Home",
  components: {},
  data() {
    return {
      enddate: "",
      startdate: "",
      asteriodData: "",
      AvgAsteriod: false,
      CloseAsteriod: false,
      FastAsteriod: false,
      Err: "",
    };
  },
  methods: {
    GetAsteriod() {
      axios
        .get(
          `https://api.nasa.gov/neo/rest/v1/feed?/?start_date=${this.startdate}&end_date=${this.enddate}&detailed=true&api_key=DEMO_KEY`,
          {
            "response.headers": ["x-ratelimit-limit"],
            method: 'POST',
            mode: 'no-cors',
            cache: 'no-cache',
            credentials: 'same-origin' ,

            headers: {
                'content-type': 'application/json'
            },
          }
        )
        .then((response) => {
          if ((response.status = 200)) {
            this.asteriodData = response.data;
            this.asteriodData = this.asteriodData.near_earth_objects;
            // console.log(JSON.parse(JSON.stringify(this.asteriodData)));
          }
        })
        .catch((err) => {
          console.log(err);
          this.Err = "The Feed date limit is only 7 Days";
        });
    },
    GetSize() {
      this.AvgAsteriod = true;
      this.CloseAsteriod = false;
      this.FastAsteriod = false;
    },
    GetClose() {
      this.CloseAsteriod = true;
      this.AvgAsteriod = false;
      this.FastAsteriod = false;
    },
    GetFast() {
      this.AvgAsteriod = false;
      this.CloseAsteriod = false;

      this.FastAsteriod = true;
    },
    
  },
  created() {
    // this.GetAsteriod();
  },
};
</script>
<style>
.select-date {
  margin: 40px 0 20px;
  display: flex;
  justify-content: space-around;
}
.end-date {
  width: 40%;
  text-align: left;
}
.start-date {
  text-align: left;

  width: 40%;
}
</style>
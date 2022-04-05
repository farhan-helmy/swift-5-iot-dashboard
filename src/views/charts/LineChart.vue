<template>
  <b-card no-body>
    <b-card-header>
      <!-- title and subtitle -->
      <div>
        <b-card-title class="mb-1"> CR1000X_315 </b-card-title>
      </div>
      <!--/ title and subtitle -->
    </b-card-header>

    <b-card-body>
      <vue-apex-charts
        type="line"
        height="400"
        :options="options"
        :series="series"
      />
    </b-card-body>
  </b-card>
</template>

<script>
import {
  BCard,
  BCardBody,
  BCardHeader,
  BCardTitle,
  BCardSubTitle,
  BBadge,
} from "bootstrap-vue";
import VueApexCharts from "vue-apexcharts";
import axios from "axios";
import apexChatData from "./apexChartData";

export default {
  components: {
    VueApexCharts,
    BCardHeader,
    BCard,
    BBadge,
    BCardBody,
    BCardTitle,
    BCardSubTitle,
  },
  data() {
    return {
      apexChatData,
      iot: [],
      data_parking: [],
      winddir: [],
      alrt_c: [],
      slrfd_w: [],
      bp_mbar: [],
      vp_mbar: [],
      rh: [],
      rain_mm_tot: [],
      options: {
        chart: {
          id: "vuechart-example",
        },
        xaxis: {
          categories: [],
        },
      },
      series: [
        {
          name: "Winddir",
          data: [],
        },
      ],
    };
  },
  mounted() {
    this.getData();
  },
  methods: {
    async getData() {
      var size = 20;
      const res = await axios.get(
        "https://mul249ynv4.execute-api.ap-southeast-1.amazonaws.com/iot/all"
      );
      //console.log(res.data.Items)
      const datas = res.data.Items;

      datas.forEach((data) => {
        const date = new Date(data.timestamp);
        //console.log(date)

        this.data_parking.push(date);
        this.winddir.push(data.winddir);
        this.alrt_c.push(data.alrt_c);
        this.slrfd_w.push(data.slrfd_w);
        this.bp_mbar.push(data.bp_mbar);
        this.vp_mbar.push(data.vp_mbar);
        this.rh.push(data.rh);
        this.rain_mm_tot.push(data.rain_mm_tot);
      });

      this.iot = this.data_parking.sort((a, b) => b.date - a.date);
      //console.log(this.iot.slice(0, size));
      console.log(this.winddir.slice(0, size));
      this.series = [
        {
          name: "Winddir",
          data: this.winddir.slice(0, size),
        },
        {
          name: "Alrt_c",
          data: this.alrt_c.slice(0, size),
        },
        {
          name: "slrfd_w",
          data: this.slrfd_w.slice(0, size),
        },
        {
          name: "bp_mbar",
          data: this.bp_mbar.slice(0, size),
        },
        {
          name: "vp_mbar",
          data: this.vp_mbar.slice(0, size),
        },
        {
          name: "rh",
          data: this.rh.slice(0, size),
        },
         {
          name: "rain_mm_tot",
          data: this.rain_mm_tot.slice(0, size),
        },
      ];

      this.options = {
        xaxis: {
          categories: this.iot.slice(0, size),
        },
      };
    },
  },
};
</script>

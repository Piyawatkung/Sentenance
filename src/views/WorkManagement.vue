<template>
  <div>
    <base-header class="pb-6 pb-8 pt-5 pt-md-8 bg-gradient-success">
      <b-col>
        <b-nav class="nav-pills justify-content-end">
          <b-nav-item
            class="mr-2 mr-md-0"
            :active="activeIndex === 0"
            link-classes="py-2 px-3"
            @click.prevent="initBigChart(0)"
          >
            <span class="d-none d-md-block"
              ><i class="ni ni-bullet-list-67 pr-2"></i>
              ดูข้อมูลในรูปแบบรายการ</span
            >
            <span class="d-md-none">M</span>
          </b-nav-item>

          <b-nav-item
            link-classes="py-2 px-3"
            :active="activeIndex === 1"
            @click.prevent="initBigChart(1)"
          >
            <span class="d-none d-md-block">
              <i class="ni ni-chart-bar-32 pr-2"></i> ดูข้อมูลในรูปแบบกราฟ</span
            >
            <span class="d-md-none">W</span>
          </b-nav-item>
        </b-nav>
      </b-col>
    </base-header>

    <b-container fluid class="mt--7">
      <b-card-text v-if="activeIndex === 0">
        <b-row>
          <b-col>
            <b-table
              id="my-table"
              striped
              :items="posts"
              :per-page="perPage"
              :current-page="currentPage"
              :fields="fields"
              small
            ></b-table>
          </b-col>
        </b-row>
        <div class="mt-3">
          <b-pagination
            v-model="currentPage"
            :total-rows="rows"
            :per-page="perPage"
            last-number
          ></b-pagination>
        </div>

        <p class="mt-3">Current Page: {{ currentPage }}</p>
      </b-card-text>

      <b-row v-if="activeIndex === 1">
        <b-col xl="12" class="mb-5 mb-xl-0">
          <card type="default" header-classes="bg-transparent">
            <b-row align-v="center" slot="header">
              <b-col>
                <h6 class="text-light text-uppercase ls-1 mb-1">Overview</h6>
                <h5 class="h3 text-white mb-0">Sales value</h5>
              </b-col>
            </b-row>
            <line-chart
              :height="350"
              ref="bigChart"
              :chart-data="bigLineChart.chartData"
              :extra-options="bigLineChart.extraOptions"
            >
            </line-chart>
          </card>
        </b-col>
      </b-row>

      <div class="mt-2"></div>
    </b-container>
  </div>
</template>

<script>
// Charts
import * as chartConfigs from "@/components/Charts/config";
import LineChart from "@/components/Charts/LineChart";

import moment from "moment";
import axios from "axios";

export default {
  components: {
    LineChart,
  },
  data() {
    return {
      rows: null,
      perPage: 20,
      currentPage: 5,
      posts: [],
      fields: [
        {
          key: "id",
        },
        {
          key: "data",
        },
        {
          key: "timestamp",
          formatter: "formatDateAssigned",
        },
        {
          key: "data2",
        },
      ],
      activeIndex: 0,
      bigLineChart: {
        allData: "",
        activeIndex: 0,
        chartData: {
          datasets: "",
          labels: "",
        },
        extraOptions: chartConfigs.blueChartOptions,
      },
    };
  },
  methods: {
    formatDateAssigned(value) {
      const formattedDate = moment(value).format("YYYY-MM-DD HH:mm:ss");
      return formattedDate;
    },

    initBigChart(index) {
      this.activeIndex = index;
    },
  },

  mounted() {
    this.initBigChart(0);
    this.chartData;
    axios
      .get(`https://swdapi.ddns.net:8090/data/ttntest`)
      .then((response) => {
        const responseData = response.data;

        // getData to List
        this.posts = responseData;
        this.rows = this.posts.length;

        this.bigLineChart.chartData = {
          labels: responseData.map((item) =>
            moment(item.timestamp).format("YYYY-MM-DD HH:mm:ss")
          ),
          allData: [
            {
              label: "Data",
              data: responseData.map((item) => item.data),
            },
          ],
          datasets: [
            {
              label: "Data",
              data: responseData.map((item) => item.data),
            },
            {
              label: "Data2",
              data: responseData.map((item) => item.data2),
            },
          ],
        };
      })
      .catch((e) => {
        this.errors.push(e);
      });
  },
};
</script>

<style>
.b-tabslist {
  z-index: 9;
  position: relative;
}

.b-tabslist .card-header {
  background-color: #fff0 !important;
  padding: 10px !important;
}

.el-table.table-dark {
  background-color: #172b4d;
  color: #f8f9fe;
}

.el-table.table-dark th,
.el-table.table-dark tr {
  background-color: #172b4d;
}

.el-table.table-dark td,
.el-table.table-dark th.is-leaf {
  border-bottom: none;
}

.table {
  background: #fff;
}

.table thead th {
  vertical-align: bottom;
  border-bottom: 2px solid #dee2e6;
}
</style>


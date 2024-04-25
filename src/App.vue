<template>
  <div class="row row-center p-a-10">
    <div class="heading">Chart Dashboard</div>
    <div class="search-input mt-20">
      <label for="search">Search:</label>
      <input
        type="text"
        id="search"
        v-model="searchText"
        @keyup="searchMethod"
        name="q"
        placeholder="Type your search term..."
      />
    </div>
  </div>
  <div
    class="row"
    v-if="getFilterArray.length > 0"
  >
    <Card
      v-for="data in getFilterArray"
      class="p-a-10 flex-g-1"
      :data="data"
    ></Card>
  </div>
  <div
    class="row"
    v-else
  >
    <h2 class="heading">No result found</h2>
  </div>
</template>
<script setup>
import axios from "axios";
import Card from "./Card.vue";
import { ref, onMounted, computed } from "vue";
onMounted(() => {
  getAllData();
});
const searchText = ref("");
const searchArray = ref([]);
const rowData = ref([
  {
    symbol: "MSFT",
    charType: "bar",
    chartData: {},
    chartOption: {},
    chartMetaData: {},
  },
  { symbol: "MSFT", charType: "line", chartData: {}, chartOption: {}, chartMetaData: {} },
  { symbol: "MSFT", charType: "candlestick", chartData: {}, chartOption: {}, chartMetaData: {} },
  { symbol: "IBM", charType: "line", chartData: {}, chartOption: {}, chartMetaData: {} },
  { symbol: "IBM", charType: "candlestick", chartData: {}, chartOption: {}, chartMetaData: {} },
  { symbol: "IBM", charType: "bar", chartData: {}, chartOption: {}, chartMetaData: {} },
]);
const getMSFTData = () => {
  return axios.get("https://www.alphavantage.co/query?function=TIME_SERIES_DAILY&symbol=MSFT&apikey=demo");
};
const getIBMData = () => {
  return axios.get("https://www.alphavantage.co/query?function=TIME_SERIES_DAILY&symbol=IBM&apikey=demo");
};
const getFilterArray = computed(() => {
  return searchText.value ? searchArray.value : rowData.value;
});
const getAllData = async () => {
  const [MSFTData, IBMData] = await Promise.all([getMSFTData(), getIBMData()]);
  getExtracDataCandle(MSFTData.data, rowData.value[2]);
  getExtracDataLine(MSFTData.data, rowData.value[1]);
  getExtracDataBar(MSFTData.data, rowData.value[0]);
  getExtracDataCandle(IBMData.data, rowData.value[4]);
  getExtracDataLine(IBMData.data, rowData.value[3]);
  getExtracDataBar(IBMData.data, rowData.value[5]);
};

const getExtracDataCandle = (MSFTData, assignObject) => {
  const seriesData = [];
  for (const key in MSFTData["Time Series (Daily)"]) {
    const data = [];
    for (const innerKey in MSFTData["Time Series (Daily)"][key]) {
      data.push(MSFTData["Time Series (Daily)"][key][innerKey]);
    }
    seriesData.push({ x: key, y: [...data] });
  }
  assignObject.chartData = [{ name: "MSFT", data: [...seriesData] }];
  assignObject.chartMetaData = getMetaData(MSFTData);
};
const getExtracDataLine = (MSFTData, assignObject) => {
  const open = [];
  const high = [];
  const low = [];
  const close = [];
  const volume = [];
  const dates = [];
  for (const key in MSFTData["Time Series (Daily)"]) {
    dates.push(key);
    for (const innerKey in MSFTData["Time Series (Daily)"][key]) {
      if (innerKey.includes("open")) {
        console.log(innerKey, "open");
        open.push(MSFTData["Time Series (Daily)"][key][innerKey]);
      } else if (innerKey.includes("low")) {
        low.push(MSFTData["Time Series (Daily)"][key][innerKey]);
      } else if (innerKey.includes("high")) {
        high.push(MSFTData["Time Series (Daily)"][key][innerKey]);
      } else if (innerKey.includes("close")) {
        close.push(MSFTData["Time Series (Daily)"][key][innerKey]);
      } else if (innerKey.includes("volume")) {
        volume.push(MSFTData["Time Series (Daily)"][key][innerKey]);
      }
    }
  }
  assignObject.chartMetaData = getMetaData(MSFTData);
  assignObject.chartData = [
    { name: "open", data: [...open] },
    { name: "low", data: [...low] },
    { name: "high", data: [...high] },
    { name: "close", data: [...close] },
    { name: "volume", data: [...volume] },
  ];
  assignObject.chartOption = {
    xaxis: {
      categories: [...dates],
    },
    stroke: {
      width: [5, 7, 5, 5],
      curve: "straight",
    },
  };
};
const getExtracDataBar = (MSFTData, assignObject) => {
  const open = [];
  const high = [];
  const low = [];
  const close = [];
  const volume = [];
  const dates = [];
  for (const key in MSFTData["Time Series (Daily)"]) {
    dates.push(key);
    for (const innerKey in MSFTData["Time Series (Daily)"][key]) {
      if (innerKey.includes("open")) {
        open.push(MSFTData["Time Series (Daily)"][key][innerKey]);
      } else if (innerKey.includes("low")) {
        low.push(MSFTData["Time Series (Daily)"][key][innerKey]);
      } else if (innerKey.includes("high")) {
        high.push(MSFTData["Time Series (Daily)"][key][innerKey]);
      } else if (innerKey.includes("close")) {
        close.push(MSFTData["Time Series (Daily)"][key][innerKey]);
      } else if (innerKey.includes("volume")) {
        volume.push(MSFTData["Time Series (Daily)"][key][innerKey]);
      }
    }
  }
  assignObject.chartMetaData = getMetaData(MSFTData);
  assignObject.chartData = [
    { name: "open", data: [...open] },
    { name: "low", data: [...low] },
    { name: "high", data: [...high] },
    { name: "close", data: [...close] },
    { name: "volume", data: [...volume] },
  ];
  assignObject.chartOption = {
    chart: {
      type: "bar",
      stacked: true,
    },
    plotOptions: {
      bar: {
        horizontal: true,
        dataLabels: {
          total: {
            enabled: false,
            offsetX: 0,
            style: {
              fontSize: "0px",
              fontWeight: 100,
            },
          },
        },
      },
    },
    dataLabels: {
      enabled: false,
      offsetX: -6,
      style: {
        fontSize: "12px",
        colors: ["#fff"],
      },
    },
    stroke: {
      show: true,
      width: 5,
    },
    tooltip: {
      shared: true,
      intersect: false,
    },
    xaxis: {
      categories: [2001, 2002, 2003, 2004, 2005, 2006, 2007],
    },
  };
};
const searchMethod = () => {
  if (searchText.value) {
    searchArray.value = rowData.value.filter((cur) => cur.symbol.toLowerCase() == searchText.value.toLowerCase() || cur.charType.toLowerCase() == searchText.value.toLowerCase());
  } else {
    searchArray.value = [];
  }
};
const getMetaData = (data) => {
  const tempObject = {};
  for (const key in data["Meta Data"]) {
    const _key = `${key.split(" ")[1]}`;
    tempObject[_key] = data["Meta Data"][key];
  }
  return tempObject;
};
</script>
<style scoped>
.row {
  display: flex;
  flex-wrap: wrap;
}
.width {
  width: 100vw;
}
.p-a-10 {
  margin: 10px;
}
.flex-g-1 {
  flex-grow: 1;
}
.row-center {
  justify-content: space-between;
}
.heading {
  font-size: 32px;
  font-weight: bold;
  color: #333; /* You can adjust the color code as per your preference */
  margin-top: 20px;
  margin-bottom: 20px;
}
body {
  font-family: "Roboto", sans-serif;
}
.mt-20 {
  margin-top: 20px;
}
</style>

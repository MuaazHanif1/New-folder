<template>
  <div class="card card-w">
    <Chart
      :data="data.chartData"
      :chartType="data.charType"
      :chartOptions="data.chartOption"
    ></Chart>
    <div class="main-content">
      <h2 class="heading">{{ data.symbol }}</h2>
      <h4 class="subtitle">{{ data.chartMetaData.Information }}</h4>
      <small class="paragraph">{{ data.chartMetaData.Last }}</small>

      <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Integer nec odio. Praesent libero. Sed cursus ante dapibus diam. Sed nisi. Nulla quis sem at nibh elementum imperdiet. Duis sagittis ipsum. Praesent mauris. Fusce nec tellus sed augue semper porta.</p>
    </div>
  </div>
</template>
<script setup>
import Chart from "./Chart.vue";
import { defineProps } from "vue";

const props = defineProps({
  title: {
    type: String,
    default: "Default Title",
  },
  subTitle: {
    type: String,
    default: "Default subTitle",
  },
  content: {
    type: String,
    default: "Default content",
    required: true,
  },
  data: {
    type: Object,
  },
});
</script>
<style scoped>
.card {
  display: flex;
  flex-direction: column;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  background-color: #fff;
  padding: 20px;
  max-width: 600px;
}
.main-content {
  display: flex;
  flex-direction: column;
}
.card-w {
  width: 400px;
}
.heading {
  font-size: 32px;
  font-weight: bold;
  color: #333; /* You can adjust the color code as per your preference */
  margin-top: 20px;
  margin-bottom: 20px;
}
.subtitle {
  font-size: 24px;
  font-weight: bold;
  color: #444; /* You can adjust the color code as per your preference */
  margin-top: 15px;
  margin-bottom: 15px;
}
.paragraph {
  font-size: 16px;
  line-height: 1.5; /* Adjust line height as needed */
  color: #666; /* You can adjust the color code as per your preference */
  margin-bottom: 20px;
}
</style>

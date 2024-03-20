<template>
  <div id="container"></div>
  <div class="searchcontain">
    <el-input
      v-model="inputvalue"
      placeholder="请输入船只编号"
      class="custom-input"
      size="large"
      clearable
    >
      <template #append>
        <el-button :icon="Search" @click="handleFormClick"
      /></template>
    </el-input>
  </div>
  <div class="input-card">
    <h5>左击获取经纬度：</h5>
    <div class="input-item">
      <input type="text" readonly="true" :value="lnglat" id="lnglat" />
    </div>
  </div>
  <MapForm :dialogData="dialogData" :isShow="isShowForm" @close="closeDialog" />
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";
import AMapLoader from "@amap/amap-jsapi-loader";
import img1 from "../assets/image/客船.png";
import img2 from "../assets/image/圆点.png";
import { Search } from "@element-plus/icons-vue";
import MapForm from "./MapForm.vue";
import axios from "axios";
// 定义经纬度的响应式变量
const lnglat = ref("");
const isShowForm = ref(false);
const inputvalue = ref("");
let dialogData = ref(null); //用来暂存获取到的数据
let map = null;
const request = axios.create({
  baseURL: "https://mock.apifox.com/m1/4197198-0-default",
  timeout: 1000,
});

const coordinates = [];
let getPointArray = async () => {
  const res = await request.get("/get_location");
  console.log("获取数据", res.data.results);
  for (let item of res.data.results) {
    const { lon, lat } = item;
    coordinates.push([lat, lon]);
  }

  console.log(coordinates);
};

getPointArray();

// 样式表
let stylesArray = [
  {
    icon: {
      img: img1,
      size: [16, 16],
      anchor: "bottom-center",
      fitZoom: 11,
      scaleFactor: 2,
      maxScale: 2,
      minScale: 1,
    },
  },
  {
    icon: {
      img: img2,
      size: [10, 10],
      anchor: "bottom-center",
      fitZoom: 10,
      scaleFactor: 2,
      maxScale: 2,
      minScale: 1,
    },
  },
];
// 小于11级使用样式1
let zoomStyleMapping = {};
for (var i = 0; i <= 20; i++) {
  if (i < 11) {
    zoomStyleMapping[i] = 1;
  } else {
    zoomStyleMapping[i] = 0;
  }
}
// 加载地图
let loadAMap = () => {
  AMapLoader.load({
    key: "44b10d242317fbe2ac98a540876f4619",
    version: "2.0",
    plugins: ["AMap.ToolBar", "AMap.Scale"],
  })
    .then((AMap) => {
      map = new AMap.Map("container", {
        viewMode: "2D",
        zoom: 5,
        center: [119.2, 34.6],
      });
      map.addControl(new AMap.ToolBar());
      map.addControl(new AMap.Scale());
      initAMapMaker(AMap);
      map.on("click", function (e) {
        lnglat.value = e.lnglat.getLng() + "," + e.lnglat.getLat();
      });
    })
    .catch((e) => {
      console.log(e);
    });
};

// 加载点标记
let initAMapMaker = (AMap) => {
  AMap.plugin(["AMap.ElasticMarker"], function () {
    for (let position of coordinates) {
      let elasticMarker = new window.AMap.ElasticMarker({
        position: position,
        styles: stylesArray,
        zoomStyleMapping: zoomStyleMapping,
      });
      map.add(elasticMarker);
      let lon = position[0];
      let lat = position[1];
      elasticMarker.on("click", async () => {
        isShowForm.value = true;
        const res = await request.get(`/get_location/119/40`);
        console.log("输出数据", res);
        dialogData.value = res.data.results;
      });
    }
  });
};
// 跳出查询表单
let handleFormClick = async () => {
  const mmsi = inputvalue.value;
  console.log(mmsi);
  if (mmsi) {
    isShowForm.value = true;
    try {
      axios({
        method: "get",
        // url: "http://localhost:5000/boats/" + mmsi,
        url: "",
      }).then((res) => {
        // isShowForm.value = true;
        dialogData.value = res.data.result;
        console.log("11", dialogData.value);
      });
    } catch (error) {
      console.error("Error fetching data:", error);
    }
  } else {
    alert("请输入船只编号");
  }
};

const closeDialog = (val) => {
  isShowForm.value = val;
};
onMounted(() => {
  loadAMap();
});

onUnmounted(() => {
  map?.destroy();
});
</script>

<style scoped>
#container {
  position: absolute;
  padding: 0px;
  margin: 0px;
  /* width: 1000px;
  height: 950px; */
  width: 100%;
  height: 100%;
  top: 0px;
  left: 0px;
}
/* 右上角白色卡片 */
.input-card {
  position: absolute;
  top: 10px;
  right: 0px;
  /* left: 760px; */
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  padding: 16px;
}

.input-card h5 {
  margin: 0;
  font-size: 16px;
  color: #333;
}

.input-item {
  height: 25px;
  margin-top: 8px;
  border-radius: 8px;
}
/* 输入框 */
input[data-v-5abf7df8] {
  border: 1px solid #ccc;
  border-radius: 6px;
  padding: 4px;
  font-size: 16px;
  outline: none;
  text-align: center;
}
/* 搜索框 */
:deep(.el-input-group__append) {
  background-color: #3498db;
}
:deep(.el-icon) {
  font-size: 20px;
}
.custom-input {
  width: 250px; /* 设置输入框宽度 */
}
.searchcontain {
  margin-top: 10px;
}
</style>
<style>
/* 隐藏左下角log后面的标志语 */
.amap-copyright {
  display: none !important;
}
</style>

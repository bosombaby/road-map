<template>
  <div class="my-dialog">
    <el-dialog
      v-model="dialogVisible"
      title="详细信息"
      @close="closeDialog"
      draggable
      width="450"
      :modal="false"
      :close-on-click-modal="false"
      class="custom-dialog"
    >
      <el-form
        :inline="true"
        :model="formInline"
        class="demo-form-inline"
        :label-position="labelPosition"
      >
        <el-form-item label="信息编号">
          <el-input v-model="formInline.id" clearable />
        </el-form-item>
        <el-form-item label="船只编号">
          <el-input v-model="formInline.mmsi" clearable />
        </el-form-item>
        <el-form-item label="纬度">
          <el-input v-model="formInline.lat" clearable />
        </el-form-item>
        <el-form-item label="经度">
          <el-input v-model="formInline.lon" clearable />
        </el-form-item>
        <el-form-item label="航速">
          <el-input v-model="formInline.speed" clearable />
        </el-form-item>
        <el-form-item label="时间">
          <el-input v-model="formInline.time" clearable />
        </el-form-item>
        <el-form-item label="航向">
          <el-input v-model="formInline.direction" clearable />
        </el-form-item>
        <el-form-item label="类型">
          <el-input v-model="formInline.type" clearable />
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>
<script setup>
import { ref, watch, reactive, toRefs, onMounted } from "vue";
const props = defineProps({
  isShow: {
    type: Boolean,
    default: false,
  },
  dialogData: {
    type: Object,
    required: true,
  },
  markerData: {
    type: Object,
    required: true,
  },
});
const { isShow, dialogData, markerData } = toRefs(props);
// 确保dialogData被解构后再使用其值初始化formInline
const formInline = reactive({
  id: "",
  mmsi: "",
  lat: "",
  lon: "",
  speed: "",
  time: "",
  direction: "",
  type: "",
});
console.log('1111',dialogData.value);
// 在钩子中初始化formInline的值，确保dialogData已准备就绪
onMounted(() => {
  // 点标记的数据
  if (dialogData.value) {
    // 点标记的数据
    
    Object.keys(formInline).forEach((key) => {
      if (dialogData.value.hasOwnProperty(key)) {
        formInline[key] = dialogData.value[key];
      }
    });
   
  } else {
    console.log("请检查数据！");
  }

  // 搜索数据
  if (dialogData.value && typeof dialogData.value === "object") {
    Object.keys(formInline).forEach((key) => {
      if (!formInline[key] && dialogData.value.hasOwnProperty(key)) {
        formInline[key] = dialogData.value[key];
      }
    });
  } else {
    console.log("请检查数据！");
  }
});

const labelPosition = ref("right");
const dialogVisible = ref(false);
const emits = defineEmits(["close"]);
const closeDialog = () => {
  emits("close", false);
};
//监听数据变化
watch(
  () => props.isShow,
  (val) => {
    // console.log(val);
    dialogVisible.value = val;
  },
  () => props.dialogData,
  () => {
    consloe.log(dialogData);
  }
);
</script>
<style scoped>
:deep(.el-dialog) {
  position: fixed;
}
:deep(.el-input__inner) {
  width: 80px;
}
:deep(.el-form-item__label) {
  width: 70px;
}
</style>

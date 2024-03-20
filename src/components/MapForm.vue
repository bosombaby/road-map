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
          <el-input v-model="formInline.id" disabled />
        </el-form-item>
        <el-form-item label="船只编号">
          <el-input v-model="formInline.mmsi" disabled />
        </el-form-item>
        <el-form-item label="纬度">
          <el-input v-model="formInline.lat" disabled />
        </el-form-item>
        <el-form-item label="经度">
          <el-input v-model="formInline.lon" disabled />
        </el-form-item>
        <el-form-item label="航速">
          <el-input v-model="formInline.speed" disabled />
        </el-form-item>
        <el-form-item label="时间">
          <el-input v-model="formInline.time" disabled />
        </el-form-item>
        <el-form-item label="航向">
          <el-input v-model="formInline.direction" disabled />
        </el-form-item>
        <el-form-item label="类型">
          <el-input v-model="formInline.type" disabled />
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>
<script setup>
import { ref, watch, reactive, toRefs, onMounted, onUpdated } from "vue";
const props = defineProps({
  isShow: {
    type: Boolean,
    default: false,
  },
  dialogData: {
    type: Object,
    required: true,
  },
});
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

// 在钩子中初始化formInline的值，确保dialogData已准备就绪

 let handleFormData = () => {
 for (let key in formInline) {
    if (props.dialogData[key]) formInline[key] = props.dialogData[key];
   }
 };

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
  }
);

onUpdated(() => {
  handleFormData();
});
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
.custom-dialog {
  background-color: aqua;
}
</style>

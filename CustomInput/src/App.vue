<template>
  <div>
    <custom-input-number :name="'inputNumber1'" :value="0" :min="0" :max="10" :step="1" :disabled="false"
      @onBlur="handleOnBlur" @onChange="handleOnChange">
    </custom-input-number>
    <hr>
    <room-allocation :guest="8" :room="4" @onChange="handleRoomAllocationOnChange">
    </room-allocation>
  </div>
</template>

<script setup>
import { onBeforeMount, onMounted, ref, reactive } from "vue";
import CustomInputNumber from "@/components/CustomInputNumber.vue";
import RoomAllocation from "@/components/RoomAllocation.vue";

const handleOnBlur = (name, value) => {
  console.log("onBlur name: " + name);
  console.log("onBlur value: " + value);
}

const handleOnChange = (name, value) => {
  console.log("onChange name: " + name);
  console.log("onChange value: " + value);
}

const handleRoomAllocationOnChange = (value) => {
  console.log(value);
}

</script>

<style lang="scss" scoped></style>

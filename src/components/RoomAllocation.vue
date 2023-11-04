<template>
    <div class="room-allocation">
        <div class="room-summary">住客人數： {{ props.guest }} 人 / {{ props.room }} 房</div>
        <div class="room-alert">尚未分配人數：{{ props.guest - totalAssignedGuests }}人</div>

        <div v-if="props.room > 0">
            <div class="room-block" v-for="(item, index) in roomAllocations">
                <div class="room-block-row">
                    <div>房間 ： {{ item.adult + item.child }}人</div>
                </div>
                <div class="room-block-row">
                    <div class="room-block-row-title">
                        <div>大人</div>
                        <span>年齡20+</span>
                    </div>
                    <custom-input-number :name="'adult-' + (index + 1)" :value="item.adult" :min="1" :max="4" :step="1"
                        :disabled="isGuestsEqualRoomNumber || isFullyAllocated" @onBlur="handleInputOnBlur"
                        @onChange="handleInputOnChange">
                    </custom-input-number>
                </div>
                <div class="room-block-row">
                    <div class="room-block-row-title">
                        <div>小孩</div>
                    </div>
                    <custom-input-number :name="'child-' + (index + 1)" :value="item.child" :min="0" :max="4" :step="1"
                        :disabled="isGuestsEqualRoomNumber || isFullyAllocated" @onBlur="handleInputOnBlur"
                        @onChange="handleInputOnChange">
                    </custom-input-number>
                </div>
            </div>
        </div>

    </div>
</template>
  
<script setup>
import { onMounted, onBeforeUnmount, ref, reactive, computed, watch } from 'vue';
import CustomInputNumber from "@/components/CustomInputNumber.vue";

const props = defineProps({
    guest: {
        type: Number,
        default: 1,
        validator: value => value >= 1,
    },
    room: {
        type: Number,
        default: 1,
        validator: value => value >= 1,
    },
})
const emit = defineEmits(["onChange"]);


const roomAllocations = ref([]);

const totalAssignedGuests = computed(() => {
    return roomAllocations.value.reduce((total, room) => total + room.adult + room.child, 0);
})

const isFullyAllocated = computed(() => {
    return totalAssignedGuests.value === props.guest;
})

const isGuestsEqualRoomNumber = computed(() => {
    return props.guest === props.room;
})

const handleInputOnBlur = (name, value) => {
    const [type, roomNumber] = name.split('-');
    if (props.guest - totalAssignedGuests.value > 0) {
        roomAllocations.value[roomNumber - 1][type] = value;
        emit('onChange', roomAllocations.value);
    }
}

const handleInputOnChange = (name, value) => {
    const [type, roomNumber] = name.split('-');
    if (props.guest - totalAssignedGuests.value > 0) {
        roomAllocations.value[roomNumber - 1][type] = value;
        emit('onChange', roomAllocations.value);
    }
}

onMounted(() => {
    //注意需使用map避免每個物件都是相同參考
    roomAllocations.value = Array(props.room).fill().map(() => ({ adult: 1, child: 0 }))
});

</script>
  
<style lang="scss" scoped>
.room-allocation {
    display: flex;
    flex-direction: column;
    width: 100%;
    max-width: 576px;

    .room-summary {
        font-size: 20px;
        font-weight: bold;
    }

    .room-alert {
        padding: 16px;
        background: rgba(135, 206, 235, 0.3);
        border: 2px solid #87ceeb;
        margin: 12px 0;
    }

    .room-block {
        border-bottom: 1px solid #BDBDBD;

        &-row {
            display: flex;
            justify-content: space-between;
            font-size: 18px;
            font-weight: bold;
            margin: 8px 0;

            &-title {
                font-size: 16px;

                span {
                    color: #757575;
                }
            }
        }
    }
}
</style>
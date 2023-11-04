<template>
    <div class="custom-input-number">
        <button class="custom-input-number-button" :name="props.name"
            :class="{ 'disabled': props.disabled || inputValue - props.step < min }" @click.prevent="decrementValue"
            @mousedown="startDecrement" @mouseup="stopChanging" @mouseleave="stopChanging">-
        </button>
        <div>
            <input type="text" v-model="inputValue" class="custom-input-number-input"
                :class="{ 'disabled': props.disabled }" :name="props.name" @input="handleInput" @blur="handleBlur" />
        </div>
        <button class="custom-input-number-button" :name="props.name"
            :class="{ 'disabled': props.disabled || inputValue + props.step > max }" @click.prevent="incrementValue"
            @mousedown="startIncrement" @mouseup="stopChanging" @mouseleave="stopChanging">+
        </button>
    </div>
</template>
<script setup>
import { onMounted, onBeforeUnmount, ref, reactive, computed, watch } from 'vue';
const props = defineProps({
    min: {
        type: Number,
        default: 0,
        validator: value => value >= 0,
    },
    max: {
        type: Number,
        default: 10,
        validator: value => value >= 0,
    },
    step: {
        type: Number,
        default: 1
    },
    name: {
        type: String,
        required: true
    },
    value: {
        type: Number,
        required: true
    },
    disabled: {
        type: Boolean,
        default: false,
    },
})

const emit = defineEmits(["onChange", "onBlur"]);

const inputValue = ref(0);
const changingInterval = ref(null);

const decrementValue = (event) => {
    if (inputValue.value > props.min) {
        let targetValue = inputValue.value - props.step;
        if (targetValue >= props.min) {
            inputValue.value = inputValue.value - props.step;
            emit('onChange', event.target.name, inputValue.value);
        }
    }
};

const incrementValue = (event) => {
    if (inputValue.value < props.max) {
        let targetValue = inputValue.value + props.step;
        if (targetValue <= props.max) {
            inputValue.value = inputValue.value + props.step;
            emit('onChange', event.target.name, inputValue.value);
        }
    }
};

const startDecrement = () => {
    changingInterval.value = setInterval(decrementValue, 300);
};

const startIncrement = () => {
    changingInterval.value = setInterval(incrementValue, 300);
};

const stopChanging = () => {
    clearInterval(changingInterval.value);
};

const handleInput = (event) => {
    let currentValue = changeValueInRange(event.target.value);
    inputValue.value = format(currentValue);
};

const handleBlur = (event) => {
    emit('onBlur', event.target.name, Number(event.target.value));
};

const format = (value) => {
    let result = value;
    if (value === null || value === undefined) {
        value = '';
    }

    if (Number.isNaN(value)) {
        result = value.toString().replace(/[^0-9]/g, '');
    } else {
        result = parseInt(value) || 0;
        result = result.toString().replace(/[^0-9]/g, '');
    }
    return Number(result);
}

const changeValueInRange = (value) => {
    if (value < props.min) {
        value = props.min;
    } else if (value > props.max) {
        value = props.max;
    }
    return value;
}

onMounted(() => {
    inputValue.value = props.value;
});

onBeforeUnmount(() => {
    clearInterval(changingInterval.value);
});
</script>
<style scoped lang="scss">
.custom-input-number {
    font-size: 16px;
    display: inline-flex;
    justify-content: center;
    align-items: center;
    gap: 8px;

    &-input {
        width: 48px;
        height: 48px;
        text-align: center;
        margin: 0;
        padding: 0;
        box-sizing: border-box;
        border: 1px solid darkgray;
        border-radius: 4px;
    }

    &-button {
        height: 48px;
        width: 48px;
        display: flex;
        justify-content: center;
        align-items: center;
        font-size: 40px;
        cursor: pointer;
        background: white;
        box-sizing: border-box;
        border: 1px solid #87ceeb;
        border-radius: 4px;
        color: #87ceeb;
    }

    .disabled {
        opacity: 0.6;
        pointer-events: none;
        cursor: not-allowed;
    }
}
</style>
<template>
    <div class="timer">
        Время: {{convertTime}}
    </div>
</template>

<script setup lang="ts">
import {computed, ref, watch} from "vue";

type TComponentProps = {
    isStart: boolean,
};

const props = defineProps<TComponentProps>();

const time = ref<number>(0);
const intervalId = ref<number | undefined>();

const convertTime = computed(() => {
    const count = 2;

    const initTime = new Date(time.value);

    const minutes = initTime.getMinutes().toString().padStart(count, '0');
    const seconds = initTime.getSeconds().toString().padStart(count, '0');
    const milliseconds = (Math.floor(initTime.getMilliseconds() / 10)).toString().padStart(count, '0');

    return `${minutes}:${seconds}:${milliseconds}`;
});

watch(() => props.isStart, (value) => {
    if (value) {
        start();
    } else {
        stop();
    }
});

function start() {
    time.value = 0;
    const tick = 10;

    intervalId.value = setInterval(() => time.value += tick, tick);
}

function stop() {
    clearInterval(intervalId.value);
}
</script>
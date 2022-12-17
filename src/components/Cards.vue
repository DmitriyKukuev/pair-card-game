<template>
    <div class="game">
        <Card v-for="item in cards"
              :key="item.id"
              :card="item"
              @open="openCard"
        />

        <div class="info-block">
            <Timer :is-start="firstClick"/>
            <div>Количество нажатий: {{clickCounter}}</div>
        </div>
    </div>
</template>

<script setup lang="ts">
import Card from './Card.vue'
import Timer from './Timer.vue'
import {computed, onMounted, ref, watch} from "vue";
import type TCard from "@/types/TCard";

onMounted(newGame);

const cards = ref<TCard[]>([]);
const firstClick = ref<boolean>(false);
const clickCounter = ref<number>(0);
const isStart = ref<boolean>(false);

const openCards = computed(() => {
    let initCards: TCard[] = [];

    cards.value.forEach((item) => {
        if (item.isOpen && !item.success) {
            initCards.push(item);
        }
    })

    return initCards;
});

const successCards = computed(() => {
    let initCards: TCard[] = [];

    cards.value.forEach((item) => {
        if (item.success) {
            initCards.push(item);
        }
    })

    return initCards;
});

watch(successCards, (value) => {
    if (value.length === cards.value.length) {
        setTimeout(newGame,1000);
    }
});

function newGame() {
    cards.value = [];

    for (let i = 0; i < 8; i++) {
        for (let j = 0; j < 2; j++) {
            cards.value.push({
                id: Math.floor(Math.random() * 10000),
                number: i + 1,
                isOpen: false,
                success: false,
            });
        }
    }

    cards.value.sort(() => Math.random() - 0.5);

    isStart.value = true;
}

function openCard(id: number) {
    checkOpenCards();

    let index = cards.value.findIndex((item) => {
        return item.id === id;
    })

    cards.value[index].isOpen = true;

    checkSuccessCards();

    firstClick.value = !(successCards.value.length === cards.value.length);

    if (isStart.value) {
        clickCounter.value = 0;
    }

    isStart.value = false;

    if (!isStart.value) {
        clickCounter.value++;
    }
}

function checkOpenCards() {
    if (openCards.value.length === 2) {
        rebootCardsOpen();
    }
}

function checkSuccessCards() {
    let cardsId = [];

    for (let i = 0; i < openCards.value.length - 1; i++) {
        if (openCards.value[i].number === openCards.value[i + 1].number) {
            cardsId.push(openCards.value[i].id);
            cardsId.push(openCards.value[i + 1].id);
        }
    }

    cardsId.forEach((item) => {
        makeSuccessCard(item);
    })
}

function makeSuccessCard(id: number) {
    let index = cards.value.findIndex((item) => {
        return item.id === id;
    });

    cards.value[index].success = true;
}

function rebootCardsOpen() {
    cards.value.forEach((item) => {
        item.isOpen = false;
    })
}
</script>
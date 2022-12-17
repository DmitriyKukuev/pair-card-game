<template>
    <div class="game__card"
         :class="{'game__card--open': card.isOpen, 'game__card--success': card.success}"
         @click="$emit('open', card.id)"
    >
        {{card.number}}
    </div>
</template>

<script setup lang="ts">
import type TCard from "@/types/TCard";

type TComponentProps = {
    card: TCard,
};

const props = defineProps<TComponentProps>();
</script>
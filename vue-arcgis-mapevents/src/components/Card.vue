<script setup lang="ts">
import { defineProps } from 'vue';

const props = defineProps({
      title: String,
      content: String,
    });


</script>

<template>
    <div>
        <p><span>{{ title }}</span> {{ content }}</p>
    </div>

</template>

<style scoped>

p {
    margin: 0;
    font-size: 20px;
}

p span {
    font-weight: bold;
}


 div {
    display: flex;
    align-content: center;
    justify-content: center;
    gap: 10px;
    border: 1px solid white;
    color: white;
    background-color: rgba(255, 255, 255, 0.2);
    padding: 10px 20px;
    height: min-content;
    width: 215px;
    border-radius: 30px;
 }

</style>
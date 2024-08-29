<script setup>
import { ref, computed } from "vue";

const totalCups = 8;
const smallCups = ref(
    Array(totalCups)
        .fill()
        .map(() => ({ full: false }))
);
const liters = ref(null);
const percentage = ref(null);
const remained = ref(null);

const fullCups = computed(() => {
    return smallCups.value.filter((cup) => cup.full).length;
});

const updateBigCup = () => {
    if (!percentage.value || !remained.value || !liters.value) return;

    const percentageHeight =
        fullCups.value === 0 ? "0" : `${(fullCups.value / totalCups) * 330}px`;
    const percentageVisibility = fullCups.value === 0 ? "hidden" : "visible";
    const remainedVisibility =
        fullCups.value === totalCups ? "hidden" : "visible";
    const remainedHeight = "0";
    const litersText = `${2 - (250 * fullCups.value) / 1000}L`;

    percentage.value.style.setProperty("visibility", percentageVisibility);
    percentage.value.style.setProperty("height", percentageHeight);
    percentage.value.innerText =
        fullCups.value === 0 ? "" : `${(fullCups.value / totalCups) * 100}%`;

    remained.value.style.setProperty("visibility", remainedVisibility);
    remained.value.style.setProperty("height", remainedHeight);
    liters.value.innerText = litersText;
};

const highlightCups = (idx) => {
    if (idx === 7 && smallCups.value[idx].full) idx--;
    else if (smallCups.value[idx].full && !smallCups.value[idx + 1]?.full) {
        idx--;
    }

    smallCups.value.forEach((cup, idx2) => {
        if (idx2 <= idx) {
            cup.full = true;
        } else {
            cup.full = false;
        }
    });

    updateBigCup();
};
</script>

<template>
    <div class="container">
        <h1>Water Intake Tracker</h1>
        <h2>Goal: 2L</h2>
        <div class="cup">
            <div class="remained" ref="remained">
                <span id="liters" ref="liters"></span>
                <small>Remained</small>
            </div>
            <div class="percentage" ref="percentage"></div>
        </div>

        <p class="text">Select how many glasses you have drunk</p>
        <div class="cups">
            <div
                v-for="(cup, index) in smallCups"
                class="cup cup-small"
                :class="{ full: cup.full }"
                @click="highlightCups(index)"
            >
                250 ml
            </div>
        </div>
    </div>
</template>

<style>
:root {
    --primary-color: #3498db;
    --secondary-color: #2980b9;
    --background-color: #ecf0f1;
    --text-color: #2c3e50;
    --cup-empty-color: #e0e0e0;
    --cup-full-color: var(--primary-color);
    --transition-speed: 0.3s;
}

* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

body {
    font-family: "Arial", sans-serif;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
    background-color: var(--background-color);
    color: var(--text-color);
}

.container {
    text-align: center;
}

h1 {
    margin: 20px 0;
    font-size: 2rem;
}

.cup {
    background-color: #fff;
    border: 4px solid var(--primary-color);
    color: var(--primary-color);
    border-radius: 0 0 40px 40px;
    height: 330px;
    width: 150px;
    margin: 30px auto;
    display: flex;
    flex-direction: column;
    overflow: hidden;
    transition: all var(--transition-speed) ease;
}

.cup.cup-small {
    width: 50px;
    height: 95px;
    border-radius: 0 0 15px 15px;
    background-color: var(--cup-empty-color);
    cursor: pointer;
    font-size: 14px;
    align-items: center;
    justify-content: center;
    text-align: center;
    margin: 5px;
    transition: all var(--transition-speed) ease;
}

.cup.cup-small.full {
    background-color: var(--cup-full-color);
    color: #fff;
}

.cups {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    width: 280px;
    margin: 0 auto;
}

.remained {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    text-align: center;
    flex: 1;
    transition: all var(--transition-speed) ease;
}

.remained span {
    font-size: 20px;
    font-weight: bold;
}

.remained small {
    font-size: 12px;
}

.percentage {
    background-color: var(--primary-color);
    color: #fff;
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: bold;
    font-size: 30px;
    height: 0;
    transition: all var(--transition-speed) ease;
}

.text {
    text-align: center;
    margin: 0 0 5px;
}

.cup.cup-small:hover {
    transform: scale(1.05);
}

@keyframes pulse {
    0% {
        transform: scale(1);
    }
    50% {
        transform: scale(1.02);
    }
    100% {
        transform: scale(1);
    }
}

.cup:not(.full) {
    animation: pulse 2s infinite;
}
</style>

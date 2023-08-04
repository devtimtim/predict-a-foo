<script setup lang="ts">
import {ref} from 'vue';

const markovChain = {
  '00': { 0: 0.7, 1: 0.3 }, // Probability of 0->0: 0.7, 0->1: 0.3
  '01': { 0: 0.5, 1: 0.5 }, // Probability of 0->0: 0.5, 0->1: 0.5
  '10': { 0: 0.6, 1: 0.4 }, // Probability of 1->0: 0.6, 1->1: 0.4
  '11': { 0: 0.4, 1: 0.6 }, // Probability of 1->0: 0.4, 1->1: 0.6
};

const clickHistory = ref([]);
const predictedClick = ref('');
const buttonClasses = ref({
  foo: {
    correct: false,
    incorrect: false,
  },
  bar: {
    correct: false,
    incorrect: false,
  },
});

function preprocessClick(buttonName: string) {
  return buttonName === 'foo' ? 1 : 0;
}

const handleButtonClick = (buttonName: string) => {
  const click = preprocessClick(buttonName);
  clickHistory.value.push(click);
  checkResult(buttonName);
  predictClick();
};

function predictClick() {
  const currentState = clickHistory.value.slice(-2).join('');
  const nextStateProbabilities = markovChain[currentState];

  // Generate a random number to choose 'foo' or 'bar' based on probabilities
  const randomNum = Math.random();

  predictedClick.value = randomNum < nextStateProbabilities[1] ? 'foo' : 'bar';

  // Display the predicted next click
  //console.log(`Predicted Next Click: ${nextClick}`);
}

function checkResult(buttonName: string) {
  if (!predictedClick.value) return;

  // Check if the last click was the same as the predicted click
  if (predictedClick.value === buttonName) {
    // console.log('Correct!', predictedClick.value, buttonName);
    buttonClasses.value[predictedClick.value].correct = true;
  } else {
    // console.log('Incorrect!', predictedClick.value, buttonName);
    buttonClasses.value[predictedClick.value].incorrect = true;
  }

  resetButtonStates();
}

function resetButtonStates() {
  setTimeout(() => {
    buttonClasses.value.foo.correct = false;
    buttonClasses.value.foo.incorrect = false;
    buttonClasses.value.bar.correct = false;
    buttonClasses.value.bar.incorrect = false;
  }, 1000);
}
</script>

<template>
  <div class="foo-bar">
    <h1>FooBar</h1>
    <p>This is a simple demonstration of AI using the <strong><a href="https://de.wikipedia.org/wiki/Markow-Kette#:~:text=Eine%20Markow%2DKette%20(englisch%20Markov,das%20Eintreten%20zuk%C3%BCnftiger%20Ereignisse%20anzugeben.">Markov Chain</a></strong> to predict clicks.<br/>Click a button a couple of times and the AI will try to learn the pattern to predict your next click.</p>
    <button id="foo" :class="{ correct: buttonClasses.foo.correct, incorrect: buttonClasses.foo.incorrect }" @click="handleButtonClick('foo')">Foo</button>
    <button id="bar" :class="{ correct: buttonClasses.bar.correct, incorrect: buttonClasses.bar.incorrect }" @click="handleButtonClick('bar')">Bar</button>
  </div>
</template>

<style scoped>
h1 {
  color: #F9f1cc;
  text-shadow: 2px 2px 0px #FFB650,
    5px 5px 0px #FFD662,
    8px 8px 0px  #FF80BF,
    11px 11px 0px #EF5097,
    14px 14px 0px #6868AC,
    17px 17px 0px #90B1E0;
}

.correct {
  position: relative;
  overflow: hidden;
}
.incorrect {
  position: relative;
  overflow: hidden;
}

.correct::after {
  animation: correct 1s;
  background-color: transparent;
  content: ' ';
  display: block;
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
.incorrect::after {
  animation: incorrect 1s;
  background-color: transparent;
  content: ' ';
  display: block;
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

@keyframes correct {
  0% {
    background-color: transparent;
  }
  50% {
    background-color: lightgreen;
  }
  100% {
    background-color: transparent;
  }
}
@keyframes incorrect {
  0% {
    background-color: transparent;
  }
  50% {
    background-color: lightcoral;
  }
  100% {
    background-color: transparent;
  }
}
</style>
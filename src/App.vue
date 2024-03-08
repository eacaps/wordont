<script setup lang="ts">
import { ref, computed } from 'vue'
import { computedAsync } from '@vueuse/core'

const words = computedAsync<string[]>(
  async () => {
    const response = await fetch("https://eacaps.github.io/wordle-words/word-list.json");
    const json = await response.json();
    return json.words;
  },
  [], // initial state
)

const c1 = ref('')
const c2 = ref('')
const c3 = ref('')
const c4 = ref('')
const c5 = ref('')

const getRegexVal = (c: string) => {
  if (c === ' ' || c === '') return '.'
  return c
}

const regex = computed(() => {
  const regex = new RegExp(
    `${getRegexVal(c1.value)}${getRegexVal(c2.value)}${getRegexVal(c3.value)}${getRegexVal(c4.value)}${getRegexVal(c5.value)}`,
    'i'
  )
  return regex
})

const dontList = computed(() => {
  return words.value
    .map((value) => {
      let active = true
      active = regex.value.test(value)
      return { value: value.toLowerCase(), active }
    })
    .sort((a, b) => {
      return a.active === b.active ? a.value.localeCompare(b.value) : a.active ? -1 : 1
    })
})
</script>

<template>
  <h1>wordont</h1>
  <div class="letters">
    <input class="char" v-model="c1" />
    <input class="char" v-model="c2" />
    <input class="char" v-model="c3" />
    <input class="char" v-model="c4" />
    <input class="char" v-model="c5" />
  </div>
  <div>
    <span v-for="word in dontList" :class="word.active ? '' : 'disabled'" :key="word.value">
      {{ word.value }}&nbsp;
    </span>
  </div>
</template>

<style scoped>
.char {
  width: 2rem;
}
.disabled {
  color: var(--vt-c-text-light-2);
}

.letters {
  padding-bottom: 0.75rem;
}
</style>

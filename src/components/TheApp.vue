<script setup lang="ts">
import { ref, computed } from 'vue'
import { computedAsync } from '@vueuse/core'

interface WordData {
  words: string[]
  lastUpdated: string
}

const wordData = computedAsync<WordData>(async () => {
  const response = await fetch('https://eacaps.github.io/wordle-words/word-list.json')
  const json = await response.json()
  return json
}, undefined)

const words = computed(() => {
  const data = wordData.value
  if (!data) return []
  return data.words
})

const lastUpdated = computed(() => {
  const data = wordData.value
  if (!data) return ''
  return data.lastUpdated
})

const letters = ref<string[]>(['', '', '', '', ''])

const setLetter = (index: number, event: Event) => {
  const input = event.target as HTMLInputElement
  // Allow only letters, keep the last typed character
  const char = input.value.replace(/[^a-zA-Z]/g, '').slice(-1)
  letters.value[index] = char
  input.value = char
}

// Only a single letter reaches the RegExp; anything else is a wildcard
const getRegexVal = (c: string) => (/^[a-zA-Z]$/.test(c) ? c : '.')

const regex = computed(() => {
  const regex = new RegExp(letters.value.map(getRegexVal).join(''), 'i')
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
  <div>
    <div class="d-flex align-items-baseline">
      <h1>wordont</h1>
      <h5 v-if="lastUpdated" class="disabled">&nbsp;as of {{ lastUpdated }}</h5>
    </div>
    <div class="letters">
      <input
        v-for="(letter, index) in letters"
        :key="index"
        class="char"
        type="text"
        maxlength="1"
        autocomplete="off"
        :aria-label="`Letter ${index + 1}`"
        :value="letter"
        @input="setLetter(index, $event)"
      />
    </div>
    <div>
      <span v-for="word in dontList" :class="word.active ? '' : 'disabled'" :key="word.value">
        {{ word.value }}&nbsp;
      </span>
    </div>
  </div>
</template>

<style scoped>
.char {
  width: 2rem;
}

.disabled {
  color: var(--color-text-muted);
}

.letters {
  padding-bottom: 0.75rem;
}
</style>

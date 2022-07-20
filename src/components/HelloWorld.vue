<template>
  <div ref="autocomplete" class="autocomplete">
    <div class="autocomplete-placeholder" v-if="search && results && !sameLength">{{autocomplete_value}}</div>
    <textarea @input="onChange" v-model="search" @keyup.down="onArrowDown" @keyup.up="onArrowUp" @keyup.right="onEnter" @keyup.tab="onEnter"/>
  </div>
</template>

<script setup>
import {defineProps, ref, onMounted, onUnmounted, computed} from 'vue';
const props = defineProps({
  items: {
    type: Array,
    required: false,
    default: () => []
  }
})
const autocomplete = ref(null);
let results = ref([]);
let search = ref("");
let arrowCounter = ref(0);

const autocomplete_value = computed(() => {
  if (results.value.length > 0) {
    const search_split = search.value.split(' ');
    return search.value + results.value[arrowCounter.value].slice(search_split[search_split.length - 1].length).toLowerCase();
  }
  return "";
})

const sameLength = computed(()=> {
  const search_split = search.value.split(' ');
  if (results.value.length > 0 && search_split.length > 0)
    return search_split[search_split.length - 1].length === results.value[arrowCounter.value].length;
  return false;
})

function onChange() {
    filterResults();
}

function filterResults() {
  const search_split = search.value.split(' ');
  if (search_split.length > 0) {
    if (search_split[search_split.length - 1] === '') {
      results.value = [];
      return;
    }
      results.value = props.items.filter(item => {
        return item.toLowerCase().indexOf(search_split[search_split.length - 1].toLowerCase()) === 0;
      });
  }
}
function onArrowDown() {
  if (arrowCounter.value < results.value.length - 1) {
    arrowCounter.value += 1;
  }
}
function onArrowUp() {
  if (arrowCounter.value > 0) {
    arrowCounter.value -= 1;
  }
}
function onEnter() {
  const search_split = search.value.split(' ');
  if (results.value.length > 0) {
    search.value = search.value + results.value[arrowCounter.value].slice(search_split[search_split.length - 1].length).toLowerCase() || "";
    arrowCounter.value = 0;
  }
}
function handleClickOutside(evt) {
  if (!autocomplete.value.contains(evt.target)) {
    arrowCounter.value = 0;
  }
}

onMounted(()=>{
  document.addEventListener("click", handleClickOutside);
})

onUnmounted(() => {
  document.removeEventListener("click", handleClickOutside);
})
</script>

<style scoped>
.autocomplete-placeholder {
  position: absolute;
  width: 100vw;
  height: 100vh;
  cursor: text;
  color: #999;
  font-size: 5em;
  padding-bottom: 1px;
  padding-top: 1px;
  font-weight: initial;
  white-space: pre-wrap;
}
textarea {
  position: absolute;
  box-sizing: border-box;
  border: 1px black solid;
  outline: none ! important;
  width: 100vw;
  height: 100vh;
  background-color: transparent;
  font-size: 5em;
  padding: 0;
  font-weight: initial;
  font-family: "Avenir", Helvetica, Arial, sans-serif;
}

.autocomplete {
  position: relative;
  height: 100vh;
  margin:1em;
}

</style>

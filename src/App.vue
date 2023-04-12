<script setup>
import { ref, computed, onMounted } from "vue";

const query = ref("");
const my_cards = ref([]);
const search_results = ref([]);
const API_KEY = import.meta.env.VITE_POKEMON_API_KEY;

const my_cards_asc = computed(() => {
  return my_cards.value.sort((a, b) => {
    return a.name.localeCompare(b.name);
  });
});

const searchCards = async (query) => {
  const url = `https://api.pokemontcg.io/v2/cards?q=${query}`;
  const response = await fetch(url, {
    headers: {
      "X-Api-Key": API_KEY,
    },
  });
  fetch(url)
    .then((res) => res.json())
    .then((res) => {
      search_results.value = res.data;
    });
};

const handleInput = (e) => {
  if (!e.target.value) {
    search_results.value = [];
  }
};

const addCard = (card) => {
  search_results.value = [];
  query.value = "";

  my_cards.value.push({
    id: card.id,
    name: card.name,
    set: card.set.name,
    totalInSet: card.set.total,
    cardNumberInSet: card.set.printedTotal,
    cardImage: card.images.large,
    cardPrice: card.tcgplayer.prices,
    owned: 0,
  });

  localStorage.setItem("my_cards", JSON.stringify(my_cards.value));
};

const increaseOwned = (card) => {
  card.owned++;
  localStorage.setItem("my_cards", JSON.stringify(my_cards.value));
};

const decreaseOwned = (card) => {
  card.owned--;
  localStorage.setItem("my_cards", JSON.stringify(my_cards.value));
};

onMounted(() => {
  my_cards.value = JSON.parse(localStorage.getItem("my_cards")) || [];
});
</script>

<template>
  <main>
    <h1>My Pokemon Collection</h1>

    <form @submit.prevent="searchCards">
      <input
        type="text"
        placeholder="What Pokemon are you looking for?"
        v-model="query"
        @input="handleInput"
      />
      <button type="submit">Search</button>
    </form>

    <div class="results" v-if="search_results.length > 0">
      <div v-bind="card in search_results">
        <img :src="card.images.large" />
        <h3>{{ card.name }}</h3>
        <button @click="addCard(card)">Add To My Collection</button>
      </div>
    </div>
  </main>
</template>

<style>
</style>

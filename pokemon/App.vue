<template>
  <div class="cards">
    <card
      v-for="starter in starters"
      @click="fetchEvolutions(starter)"
      :class="{ apace: selectedId && starter.id !== selectedId }"
      class="card"
    >
      <template v-slot:title> {{ starter.name }} #{{ starter.id }} </template>
      <template v-slot:content>
        <img :src="starter.sprite" alt="" />
      </template>
      <template v-slot:description>
        <div v-for="type in starter.types">
          {{ type }}
        </div>
      </template>
    </card>
  </div>

  <div class="cards">
    <card v-for="creature in evolutions">
      <template v-slot:title> {{ creature.name }} #{{ creature.id }} </template>
      <template v-slot:content>
        <img :src="creature.sprite" alt="" />
      </template>
      <template v-slot:description>
        <div v-for="type in creature.types">
          {{ type }}
        </div>
      </template>
    </card>
  </div>
</template>
 
<script>
import Card from "./card.vue";
const api = "https://pokeapi.co/api/v2/pokemon";
const STARTER_IDS = [1, 4, 7];
export default {
  components: {
    Card,
  },
  data() {
    return {
      starters: [],
      evolutions: [],
      selectedId: null,
    };
  },
  async created() {
    const starters = await this.fetchData(STARTER_IDS);
    this.starters = starters;
  },
  methods: {
    async fetchData(ids) {
      const responses = await Promise.all(
        ids.map((id) => window.fetch(`${api}/${id}`))
      );
      const data = await Promise.all(responses.map((res) => res.json()));
      return data.map((datum) => ({
        id: datum.id,
        name: datum.name,
        sprite: datum.sprites.other["official-artwork"].front_default,
        types: datum.types.map((type) => type.type.name),
      }));
    },
    async fetchEvolutions(pokemon) {
      this.selectedId = pokemon.id;
      this.evolutions = await this.fetchData([pokemon.id + 1, pokemon.id + 2]);
    },
  },
};
</script>

<style scoped>
.cards {
  display: flex;
}
.card:hover{
  opacity: 1.0;
}
.apace {
  opacity: 0.5;
}
img {
  width: 100%;
}
</style>
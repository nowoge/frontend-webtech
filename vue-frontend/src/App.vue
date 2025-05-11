<script setup>
import { ref } from 'vue';
import RecipeCard from './components/RecipeCard.vue'; // Importiere die Rezeptkarte

const recipes = ref([
  {
    id: 1,
    name: 'Kartoffelgratin',
    ingredients: [
      { name: 'Kochsahne', amount: '200g' },
      { name: 'Kartoffel', amount: '400g' },
      { name: 'Käse', amount: '75g' },
      { name: 'Muskatnuss', amount: '1 Prise' },
    ],
    instructions: `Kochen`,
    preparationTime: '45 Minuten',
    imageUrl: ''
  },

]);

// Später: function addRecipe() { recipes.value.push({ ...neuesRezeptObjekt... });}

</script>

<template>
  <div class="app-container">
    <header>
      <h1>Meine Rezeptsammlung</h1>
    </header>
    <main class="recipes-grid">
      <RecipeCard
        v-for="recipe in recipes"
        :key="recipe.id"
        :id="recipe.id"
        :name="recipe.name"
        :ingredients="recipe.ingredients"
        :instructions="recipe.instructions"
        :preparation-time="recipe.preparationTime"
        :image-url="recipe.imageUrl"
      />
      <p v-if="recipes.length === 0">Noch keine Rezepte vorhanden.</p>
    </main>
  </div>
</template>

<style>
body {
  font-family: 'Arial', sans-serif;
  margin: 0;
  background-color: #f4f7f6;
  color: #333;
}

.app-container header {
  background-color: #4CAF50;
  color: white;
  padding: 20px 0;
  text-align: center;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.app-container header h1 {
  margin: 0;
  font-size: 2.5em;
}

.recipes-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 20px;
  padding: 20px;
  max-width: 1200px;
  margin: 20px auto;
}


@media (max-width: 600px) {
  .recipes-grid {
    grid-template-columns: 1fr;
  }
}
</style>

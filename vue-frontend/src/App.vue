<script setup>
import { ref, onMounted } from 'vue';
import RecipeCard from './components/RecipeCard.vue';

const recipes = ref([]);
const isLoading = ref(false);
const error = ref(null);

// Funktion zum Abrufen der Daten vom Backend
async function fetchRecipes() {
  isLoading.value = true;
  error.value = null;
  try {
    const baseURL = import.meta.env.VITE_APP_BACKEND_BASE_URL;
    if (!baseURL) {
      console.error("VITE_APP_BACKEND_BASE_URL ist nicht in den .env Dateien definiert!");
      throw new Error("Backend-URL nicht konfiguriert.");
    }
    const endpoint = `${baseURL}/webtech`;

    const requestOptions = {
      method: 'GET',
      redirect: 'follow'
    };

    // Den fetch-Aufruf mit await versehen, um die Response-Variable zu bekommen
    const response = await fetch(endpoint, requestOptions);

    if (!response.ok) {
      let errorMessage = `HTTP error! status: ${response.status}`;
      try {
        const errorBody = await response.text();
        errorMessage += ` - ${errorBody}`;
      } catch (e) {

      }
      throw new Error(errorMessage);
    }

    const backendRecipeData = await response.json();


    const transformedRecipe = {
      id: backendRecipeData.id,
      name: backendRecipeData.name,
      ingredients: Array.isArray(backendRecipeData.ingredients)
        ? backendRecipeData.ingredients.map(ingredientName => ({
          name: ingredientName,
          amount: ''
        }))
        : [],
      instructions: backendRecipeData.instructions,
      preparationTime: backendRecipeData.preparationTime,
      imageUrl: backendRecipeData.imageUrl || ''
    };

    recipes.value = [transformedRecipe];

  } catch (e) {
    console.error("Fehler beim Abrufen der Rezepte:", e);
    error.value = "Rezepte konnten nicht geladen werden: " + e.message;
    recipes.value = [];
  } finally {
    isLoading.value = false;
  }
}
onMounted(() => {
  fetchRecipes();
});

</script>

<template>
  <div class="app-container">
    <header>
      <h1>Meine Rezeptsammlung</h1>
    </header>
    <main class="recipes-grid">
      <div v-if="isLoading">Lade Rezepte...</div>
      <div v-if="error" style="color: red;">{{ error }}</div>
      <template v-if="!isLoading && !error">
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
        <p v-if="recipes.length === 0 && !isLoading">Noch keine Rezepte vorhanden.</p>
      </template>
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

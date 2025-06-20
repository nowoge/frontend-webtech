<script setup>
import { ref, onMounted } from 'vue';
import RecipeCard from './components/RecipeCard.vue';

// === BESTEHENDER ZUSTAND ===
const recipes = ref([]);
const isLoading = ref(false);
const error = ref(null);

// === NEUER ZUSTAND FÜR DAS FORMULAR ===
const isSubmitting = ref(false);
const newRecipe = ref({
  name: '',
  // Wir nutzen ein Textfeld für die Zutaten, die durch Kommas getrennt werden
  ingredients: '',
  instructions: '',
  preparationTime: '',
  imageUrl: ''
});

// Helper-Funktion, um Codeduplizierung zu vermeiden
function transformBackendRecipe(backendRecipeData) {
  return {
    id: backendRecipeData.id,
    name: backendRecipeData.name,
    // Transformiert das String-Array vom Backend in das vom Frontend benötigte Objekt-Array
    ingredients: Array.isArray(backendRecipeData.ingredients)
      ? backendRecipeData.ingredients.map(ingredientName => ({
        name: ingredientName,
        amount: '' // amount ist hier nur ein Frontend-Platzhalter
      }))
      : [],
    instructions: backendRecipeData.instructions,
    preparationTime: backendRecipeData.preparationTime,
    imageUrl: backendRecipeData.imageUrl || 'https://via.placeholder.com/300' // Standardbild
  };
}

// Get-Funktion
async function fetchRecipes() {
  isLoading.value = true;
  error.value = null;
  try {
    const baseURL = import.meta.env.VITE_APP_BACKEND_BASE_URL;
    if (!baseURL) throw new Error("Backend-URL nicht konfiguriert.");

    const endpoint = `${baseURL}/webtech`;
    const response = await fetch(endpoint, { method: 'GET' });

    if (!response.ok) throw new Error(`HTTP-Fehler! Status: ${response.status}`);

    const backendRecipeData = await response.json();

    recipes.value = [transformBackendRecipe(backendRecipeData)];

  } catch (e) {
    console.error("Fehler beim Abrufen der Rezepte:", e);
    error.value = "Rezepte konnten nicht geladen werden: " + e.message;
  } finally {
    isLoading.value = false;
  }
}

// Post-Funktion
async function addRecipe() {
  isSubmitting.value = true;
  error.value = null;

  try {
    const baseURL = import.meta.env.VITE_APP_BACKEND_BASE_URL;
    if (!baseURL) throw new Error("Backend-URL nicht konfiguriert.");


    const payload = {
      name: newRecipe.value.name,
      ingredients: newRecipe.value.ingredients.split(',').map(item => item.trim()).filter(item => item),
      instructions: newRecipe.value.instructions,
      preparationTime: newRecipe.value.preparationTime,
      imageUrl: newRecipe.value.imageUrl
    };


    const endpoint = `${baseURL}/webtech`;
    const response = await fetch(endpoint, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(payload)
    });

    if (!response.ok) {
      throw new Error(`Rezept konnte nicht erstellt werden. Status: ${response.status}`);
    }

    const createdRecipe = await response.json();

    recipes.value.push(transformBackendRecipe(createdRecipe));

    newRecipe.value = { name: '', ingredients: '', instructions: '', preparationTime: '', imageUrl: '' };

  } catch (e) {
    console.error("Fehler beim Hinzufügen des Rezepts:", e);
    error.value = "Fehler: " + e.message;
  } finally {
    isSubmitting.value = false;
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

    <section class="add-recipe-form">
      <h2>Neues Rezept hinzufügen</h2>
      <form @submit.prevent="addRecipe">
        <div class="form-group">
          <label for="name">Name des Rezepts</label>
          <input type="text" id="name" v-model="newRecipe.name" required>
        </div>
        <div class="form-group">
          <label for="ingredients">Zutaten (durch Komma getrennt)</label>
          <input type="text" id="ingredients" v-model="newRecipe.ingredients" placeholder="z.B. Mehl, Zucker, Eier" required>
        </div>
        <div class="form-group">
          <label for="instructions">Anleitung</label>
          <textarea id="instructions" v-model="newRecipe.instructions" rows="4" required></textarea>
        </div>
        <div class="form-group">
          <label for="preparationTime">Zubereitungszeit</label>
          <input type="text" id="preparationTime" v-model="newRecipe.preparationTime" placeholder="z.B. 45 Minuten" required>
        </div>
        <div class="form-group">
          <label for="imageUrl">Bild-URL (optional)</label>
          <input type="url" id="imageUrl" v-model="newRecipe.imageUrl" placeholder="https://beispiel.com/bild.jpg">
        </div>
        <button type="submit" :disabled="isSubmitting">
          {{ isSubmitting ? 'Wird gespeichert...' : 'Rezept hinzufügen' }}
        </button>
      </form>
    </section>

    <!-- BESTEHENDE REZEPTANZEIGE -->
    <main class="recipes-grid">
      <div v-if="isLoading">Lade Rezepte...</div>
      <div v-if="error" class="error-message">{{ error }}</div>
      <template v-if="!isLoading">
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

.error-message {
  color: white;
  background-color: #e53935;
  padding: 10px;
  border-radius: 4px;
  text-align: center;
  grid-column: 1 / -1;
}


.add-recipe-form {
  max-width: 600px;
  margin: 30px auto;
  padding: 20px;
  background-color: white;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}

.add-recipe-form h2 {
  text-align: center;
  margin-top: 0;
  color: #333;
}

.form-group {
  margin-bottom: 15px;
}

.form-group label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

.form-group input,
.form-group textarea {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box; /* Wichtig, damit padding nicht die Breite beeinflusst */
}

.add-recipe-form button {
  width: 100%;
  padding: 12px;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 1em;
  font-weight: bold;
}

.add-recipe-form button:disabled {
  background-color: #aaa;
  cursor: not-allowed;
}

@media (max-width: 600px) {
  .recipes-grid {
    grid-template-columns: 1fr;
  }
}
</style>

<template>
  <div
    class="min-vh-100 bg-cover bg-center"
    :style="{ backgroundImage: `url(${backgroundImage})` }"
  >
    <div class="min-vh-100" :class="darkMode ? 'bg-dark bg-opacity-75' : 'bg-light bg-opacity-75'">
      <!-- Navigation -->
      <nav class="navbar navbar-expand-lg" :class="darkMode ? 'navbar-dark bg-dark bg-opacity-90' : 'navbar-light bg-light bg-opacity-90'">
        <div class="container-fluid px-5">
          <a class="navbar-brand d-flex align-items-center" href="#" @click.prevent="currentPage = 'home'">
            <span class="fs-2 me-2">🍳</span>
            <span class="fw-bold text-purple fs-3">MeineRezeptesammlung</span>
          </a>

          <div class="navbar-nav mx-auto">
            <button
              @click="currentPage = 'home'"
              :class="['btn', 'me-4', 'px-4', 'py-2', currentPage === 'home' ? 'btn-purple' : (darkMode ? 'btn-outline-light' : 'btn-outline-dark')]"
            >
              Home
            </button>
            <button
              @click="currentPage = 'about'"
              :class="['btn', 'me-4', 'px-4', 'py-2', currentPage === 'about' ? 'btn-purple' : (darkMode ? 'btn-outline-light' : 'btn-outline-dark')]"
            >
              About
            </button>
            <button
              @click="currentPage = 'recipes'"
              :class="['btn', 'me-4', 'px-4', 'py-2', currentPage === 'recipes' ? 'btn-purple' : (darkMode ? 'btn-outline-light' : 'btn-outline-dark')]"
            >
              Recipe
            </button>
            <button
              @click="currentPage = 'search'"
              :class="['btn', 'me-4', 'px-4', 'py-2', currentPage === 'search' ? 'btn-purple' : (darkMode ? 'btn-outline-light' : 'btn-outline-dark')]"
            >
              Search
            </button>
          </div>

          <button
            @click="toggleDarkMode"
            :class="['btn', 'px-3', 'py-2', darkMode ? 'btn-outline-light' : 'btn-outline-dark']"
          >
            {{ darkMode ? '☀️' : '🌙' }}
          </button>
        </div>
      </nav>

      <!-- Content -->
      <div class="container-fluid px-5 py-5">
        <!-- Home Page -->
        <div v-if="currentPage === 'home'" class="text-center">
          <div class="row justify-content-center">
            <div class="col-xl-10 col-lg-12">
              <h1 class="display-1 fw-bold mb-5" :class="darkMode ? 'text-light' : 'text-dark'" style="font-size: 5rem;">
                Wilkommen zu <span class="text-purple">MeineRezeptesammlung</span>
              </h1>
              <p class="lead mb-5" :class="darkMode ? 'text-light' : 'text-dark'" style="font-size: 2rem;">
                Entdecke, erstelle und sammle fantastische Rezepte
              </p>
              <button
                @click="currentPage = 'recipes'"
                class="btn btn-purple btn-lg px-5 py-3"
                style="font-size: 1.5rem;"
              >
                Jetzt Erstellen/Kochen
              </button>
            </div>
          </div>
        </div>

        <!-- About Page -->
        <div v-if="currentPage === 'about'" class="row justify-content-center">
          <div class="col-xl-10 col-lg-12">
            <h1 class="display-2 fw-bold text-center mb-5" :class="darkMode ? 'text-light' : 'text-dark'">
              About MeineRezeptesammlung
            </h1>
            <div class="row">
              <div class="col-lg-8 mx-auto">
                <div class="card shadow-lg" :class="darkMode ? 'bg-dark text-light' : 'bg-light'">
                  <div class="card-body p-5">
                    <p class="fs-4 mb-4">
                      MeineRezeptesammlung ist eine Plattform für Kochbegeisterte, die ihre Lieblingsrezepte verwalten und neue entdecken möchten.
                    </p>
                    <p class="fs-5 mb-4">
                      Unsere Mission ist es, das Kochen einfacher und zugänglicher zu machen. Egal ob Anfänger oder Profi - hier findest du Rezepte für jeden Geschmack.
                    </p>
                    <p class="fs-5">
                      Entwickelt von <strong class="text-purple">Noel</strong> und <strong class="text-purple">Dilara</strong> mit viel Liebe zum Detail.
                    </p>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Recipes Page -->
        <div v-if="currentPage === 'recipes'">
          <h1 class="display-3 fw-bold text-center mb-5" :class="darkMode ? 'text-light' : 'text-dark'">
            Rezepte
          </h1>

          <div class="row">
            <!-- Formular -->
            <div class="col-xl-4 col-lg-5 mb-4">
              <div class="card shadow-lg h-100" :class="darkMode ? 'bg-dark text-light' : 'bg-light'">
                <div class="card-body p-4">
                  <h2 class="card-title text-purple mb-4 fs-3">Neues Rezept</h2>

                  <!-- Loading/Error Messages -->
                  <div v-if="isSubmitting" class="alert alert-info">
                    <div class="d-flex align-items-center">
                      <div class="spinner-border spinner-border-sm me-2" role="status"></div>
                      Wird gespeichert...
                    </div>
                  </div>

                  <div v-if="error" class="alert alert-danger">
                    {{ error }}
                  </div>

                  <form @submit.prevent="addRecipe">
                    <div class="mb-3">
                      <label class="form-label fs-6">Rezept Name</label>
                      <input
                        type="text"
                        v-model="newRecipe.name"
                        class="form-control form-control-lg"
                        :class="darkMode ? 'bg-secondary text-light' : ''"
                        required
                      />
                    </div>

                    <div class="mb-3">
                      <label class="form-label fs-6">Zutaten (mit Komma getrennt)</label>
                      <input
                        type="text"
                        v-model="newRecipe.ingredients"
                        class="form-control form-control-lg"
                        :class="darkMode ? 'bg-secondary text-light' : ''"
                        required
                      />
                    </div>

                    <div class="mb-3">
                      <label class="form-label fs-6">Anleitung</label>
                      <textarea
                        v-model="newRecipe.instructions"
                        class="form-control form-control-lg"
                        :class="darkMode ? 'bg-secondary text-light' : ''"
                        rows="4"
                        required
                      ></textarea>
                    </div>

                    <div class="mb-3">
                      <label class="form-label fs-6">Zubereitungszeit</label>
                      <input
                        type="text"
                        v-model="newRecipe.preparationTime"
                        class="form-control form-control-lg"
                        :class="darkMode ? 'bg-secondary text-light' : ''"
                        required
                      />
                    </div>

                    <div class="mb-4">
                      <label class="form-label fs-6">Bild URL (optional)</label>
                      <input
                        type="url"
                        v-model="newRecipe.imageUrl"
                        class="form-control form-control-lg"
                        :class="darkMode ? 'bg-secondary text-light' : ''"
                      />
                    </div>

                    <button
                      type="submit"
                      class="btn btn-purple w-100 btn-lg"
                      :disabled="isSubmitting"
                    >
                      <span v-if="isSubmitting">
                        <div class="spinner-border spinner-border-sm me-2" role="status"></div>
                        Wird gespeichert...
                      </span>
                      <span v-else>
                        ➕ Rezept hinzufügen
                      </span>
                    </button>
                  </form>
                </div>
              </div>
            </div>

            <!-- Rezepte Liste -->
            <div class="col-xl-8 col-lg-7">
              <div v-if="isLoading" class="text-center">
                <div class="spinner-border text-purple" role="status" style="width: 3rem; height: 3rem;"></div>
                <p class="mt-3 fs-4" :class="darkMode ? 'text-light' : 'text-dark'">Lade Rezepte...</p>
              </div>

              <div v-else-if="recipes.length === 0" class="text-center">
                <div class="card shadow" :class="darkMode ? 'bg-dark text-light' : 'bg-light'">
                  <div class="card-body py-5">
                    <h3 class="fs-2">Noch keine Rezepte vorhanden</h3>
                    <p class="fs-4">Füge dein erstes Rezept hinzu!</p>
                  </div>
                </div>
              </div>

              <div v-else class="row">
                <div v-for="recipe in recipes" :key="recipe.id" class="col-xl-6 col-lg-12 col-md-6 mb-4">
                  <div class="card shadow-lg h-100" :class="darkMode ? 'bg-dark text-light' : 'bg-light'">
                    <img
                      :src="recipe.imageUrl || 'https://images.pexels.com/photos/1640777/pexels-photo-1640777.jpeg?auto=compress&cs=tinysrgb&w=400'"
                      :alt="recipe.name"
                      class="card-img-top"
                      style="height: 250px; object-fit: cover;"
                    />
                    <div class="card-body p-4">
                      <h5 class="card-title text-purple fs-4">{{ recipe.name }}</h5>
                      <p class="text-muted mb-3 fs-6">
                        <strong>Zeit:</strong> {{ recipe.preparationTime }}
                      </p>

                      <div class="mb-3">
                        <h6 class="fw-bold fs-6">Zutaten:</h6>
                        <ul class="list-unstyled">
                          <li v-for="(ing, i) in recipe.ingredients.slice(0, 3)" :key="i" class="fs-6">
                            • {{ ing }}
                          </li>
                          <li v-if="recipe.ingredients.length > 3" class="fs-6 text-purple">
                            • +{{ recipe.ingredients.length - 3 }} weitere
                          </li>
                        </ul>
                      </div>

                      <p class="card-text fs-6">
                        {{ recipe.instructions.substring(0, 120) }}...
                      </p>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Search Page -->
        <div v-if="currentPage === 'search'">
          <h1 class="display-3 fw-bold text-center mb-5" :class="darkMode ? 'text-light' : 'text-dark'">
            Rezepte suchen
          </h1>

          <div class="row justify-content-center mb-5">
            <div class="col-xl-8 col-lg-10">
              <div class="card shadow" :class="darkMode ? 'bg-dark' : 'bg-light'">
                <div class="card-body p-4">
                  <div class="input-group input-group-lg">
                    <span class="input-group-text fs-4" :class="darkMode ? 'bg-secondary text-light' : ''">🔍</span>
                    <input
                      type="text"
                      v-model="searchTerm"
                      class="form-control form-control-lg"
                      :class="darkMode ? 'bg-secondary text-light' : ''"
                      placeholder="Suche nach Rezepten oder Zutaten..."
                      style="font-size: 1.2rem;"
                    />
                  </div>
                </div>
              </div>
            </div>
          </div>

          <div v-if="searchTerm && filteredRecipes.length === 0" class="text-center">
            <div class="card shadow" :class="darkMode ? 'bg-dark text-light' : 'bg-light'">
              <div class="card-body py-5">
                <h3 class="fs-2">Keine Rezepte gefunden</h3>
                <p class="fs-4">Für "{{ searchTerm }}" wurden keine Rezepte gefunden.</p>
              </div>
            </div>
          </div>

          <div v-else-if="!searchTerm && recipes.length === 0" class="text-center">
            <div class="card shadow" :class="darkMode ? 'bg-dark text-light' : 'bg-light'">
              <div class="card-body py-5">
                <h3 class="fs-2">Noch keine Rezepte vorhanden</h3>
                <p class="fs-4">Füge erst Rezepte hinzu, um sie durchsuchen zu können.</p>
              </div>
            </div>
          </div>

          <div v-else class="row">
            <div v-for="recipe in filteredRecipes" :key="recipe.id" class="col-xl-4 col-lg-6 col-md-6 mb-4">
              <div class="card shadow-lg h-100" :class="darkMode ? 'bg-dark text-light' : 'bg-light'">
                <img
                  :src="recipe.imageUrl || 'https://images.pexels.com/photos/1640777/pexels-photo-1640777.jpeg?auto=compress&cs=tinysrgb&w=400'"
                  :alt="recipe.name"
                  class="card-img-top"
                  style="height: 250px; object-fit: cover;"
                />
                <div class="card-body p-4">
                  <h5 class="card-title text-purple fs-4">{{ recipe.name }}</h5>
                  <p class="text-muted mb-3 fs-6">
                    <strong>Zeit:</strong> {{ recipe.preparationTime }}
                  </p>

                  <div class="mb-3">
                    <h6 class="fw-bold fs-6">Zutaten:</h6>
                    <ul class="list-unstyled">
                      <li v-for="(ing, i) in recipe.ingredients.slice(0, 3)" :key="i" class="fs-6">
                        • {{ ing }}
                      </li>
                    </ul>
                  </div>

                  <p class="card-text fs-6">
                    {{ recipe.instructions.substring(0, 100) }}...
                  </p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from 'vue'

// Reactive state
const darkMode = ref(false)
const currentPage = ref('home')
const recipes = ref([])
const searchTerm = ref('')
const isLoading = ref(false)
const isSubmitting = ref(false)
const error = ref(null)

// Neues Rezept Formular
const newRecipe = ref({
  name: '',
  ingredients: '',
  instructions: '',
  preparationTime: '',
  imageUrl: ''
})

// Computed
const backgroundImage = computed(() => {
  return darkMode.value
    ? 'https://images.pexels.com/photos/1565982/pexels-photo-1565982.jpeg?auto=compress&cs=tinysrgb&w=1920'
    : 'https://images.pexels.com/photos/1640777/pexels-photo-1640777.jpeg?auto=compress&cs=tinysrgb&w=1920'
})

const filteredRecipes = computed(() => {
  if (!searchTerm.value) return recipes.value

  return recipes.value.filter(recipe =>
    recipe.name.toLowerCase().includes(searchTerm.value.toLowerCase()) ||
    recipe.ingredients.some(ing => ing.toLowerCase().includes(searchTerm.value.toLowerCase()))
  )
})

// Helper-Funktion für Backend-Transformation
function transformBackendRecipe(backendRecipeData) {
  return {
    id: backendRecipeData.id,
    name: backendRecipeData.name,
    ingredients: Array.isArray(backendRecipeData.ingredients)
      ? backendRecipeData.ingredients
      : [],
    instructions: backendRecipeData.instructions,
    preparationTime: backendRecipeData.preparationTime,
    imageUrl: backendRecipeData.imageUrl || 'https://images.pexels.com/photos/1640777/pexels-photo-1640777.jpeg?auto=compress&cs=tinysrgb&w=400'
  }
}

// Methods
const toggleDarkMode = () => {
  darkMode.value = !darkMode.value
}

// Backend: Rezepte laden
async function fetchRecipes() {
  isLoading.value = true
  error.value = null

  try {
    const baseURL = import.meta.env.VITE_APP_BACKEND_BASE_URL
    if (!baseURL) throw new Error("Backend-URL nicht konfiguriert.")

    const endpoint = `${baseURL}/webtech`
    const response = await fetch(endpoint, { method: 'GET' })

    if (!response.ok) throw new Error(`HTTP-Fehler! Status: ${response.status}`)

    const backendRecipeData = await response.json()

    // Wenn es ein einzelnes Rezept ist, in Array umwandeln
    if (Array.isArray(backendRecipeData)) {
      recipes.value = backendRecipeData.map(transformBackendRecipe)
    } else {
      recipes.value = [transformBackendRecipe(backendRecipeData)]
    }

  } catch (e) {
    console.error("Fehler beim Abrufen der Rezepte:", e)
    error.value = "Rezepte konnten nicht geladen werden: " + e.message
  } finally {
    isLoading.value = false
  }
}

// Backend: Rezept hinzufügen
async function addRecipe() {
  isSubmitting.value = true
  error.value = null

  try {
    const baseURL = import.meta.env.VITE_APP_BACKEND_BASE_URL
    if (!baseURL) throw new Error("Backend-URL nicht konfiguriert.")

    const payload = {
      name: newRecipe.value.name,
      ingredients: newRecipe.value.ingredients.split(',').map(item => item.trim()).filter(item => item),
      instructions: newRecipe.value.instructions,
      preparationTime: newRecipe.value.preparationTime,
      imageUrl: newRecipe.value.imageUrl
    }

    const endpoint = `${baseURL}/webtech`
    const response = await fetch(endpoint, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(payload)
    })

    if (!response.ok) {
      throw new Error(`Rezept konnte nicht erstellt werden. Status: ${response.status}`)
    }

    const createdRecipe = await response.json()
    recipes.value.push(transformBackendRecipe(createdRecipe))

    // Formular zurücksetzen
    newRecipe.value = {
      name: '',
      ingredients: '',
      instructions: '',
      preparationTime: '',
      imageUrl: ''
    }

  } catch (e) {
    console.error("Fehler beim Hinzufügen des Rezepts:", e)
    error.value = "Fehler: " + e.message
  } finally {
    isSubmitting.value = false
  }
}

// Lifecycle
onMounted(() => {
  // Dark Mode aus localStorage laden
  const savedDarkMode = localStorage.getItem('darkMode')
  if (savedDarkMode) {
    darkMode.value = JSON.parse(savedDarkMode)
  }

  // Rezepte vom Backend laden
  fetchRecipes()
})

// Watchers
watch(darkMode, (newValue) => {
  localStorage.setItem('darkMode', JSON.stringify(newValue))
  document.documentElement.classList.toggle('dark', newValue)
})
</script>

<style>
/* Bootstrap CSS */
@import 'https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css';

/* Lila Theme */
:root {
  --purple-color: #8b5cf6;
  --purple-hover: #7c3aed;
  --purple-light: #a78bfa;
}

.text-purple {
  color: var(--purple-color) !important;
}

.btn-purple {
  background-color: var(--purple-color);
  border-color: var(--purple-color);
  color: white;
}

.btn-purple:hover {
  background-color: var(--purple-hover);
  border-color: var(--purple-hover);
  color: white;
}

.btn-purple:focus {
  box-shadow: 0 0 0 0.2rem rgba(139, 92, 246, 0.25);
}

.btn-purple:disabled {
  background-color: var(--purple-light);
  border-color: var(--purple-light);
}

/* Desktop-optimierte Größen */
.container-fluid {
  max-width: 1400px;
}

/* Hintergrund Styles */
.bg-cover {
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  background-attachment: fixed;
}

/* Card Hover Effects */
.card {
  transition: transform 0.2s ease-in-out;
}

.card:hover {
  transform: translateY(-5px);
}

/* Dark Mode Anpassungen */
.bg-dark.bg-opacity-75 {
  background-color: rgba(33, 37, 41, 0.75) !important;
}

.bg-light.bg-opacity-75 {
  background-color: rgba(248, 249, 250, 0.75) !important;
}

.navbar.bg-dark.bg-opacity-90 {
  background-color: rgba(33, 37, 41, 0.9) !important;
}

.navbar.bg-light.bg-opacity-90 {
  background-color: rgba(248, 249, 250, 0.9) !important;
}

/* Responsive Anpassungen für große Bildschirme */
@media (min-width: 1200px) {
  .display-1 {
    font-size: 5rem !important;
  }

  .lead {
    font-size: 2rem !important;
  }
}

@media (min-width: 1400px) {
  .container-fluid {
    padding-left: 5rem !important;
    padding-right: 5rem !important;
  }
}
</style>

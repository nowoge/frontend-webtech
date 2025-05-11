<script setup>
import { computed } from 'vue';

const props = defineProps({
  id: {
    type: [String, Number],
    required: true
  },
  name: {
    type: String,
    required: true
  },
  ingredients: {
    type: Array,
    required: true,
    default: () => []
  },
  instructions: {
    type: String,
    required: true
  },
  preparationTime: {
    type: String,
    required: true
  },

  imageUrl: {
    type: String,
    default: ''
  }
});

const formattedIngredients = computed(() => {
  return props.ingredients.map(ing => {
    if (typeof ing === 'object' && ing !== null && ing.name) {
      return `${ing.amount || ''} ${ing.name}`.trim();
    }
    return ing;
  });
});


const formattedInstructions = computed(() => {
  return props.instructions.replace(/\n/g, '<br>');
});
</script>

<template>
  <div class="recipe-card">
    <img v-if="imageUrl" :src="imageUrl" :alt="`Bild von ${name}`" class="recipe-image">
    <div class="recipe-content">
      <h2 class="recipe-name">{{ name }}</h2>
      <p class="preparation-time">
        <strong>Zubereitungszeit:</strong> {{ preparationTime }}
      </p>

      <div class="recipe-section">
        <h3>Zutaten:</h3>
        <ul v-if="formattedIngredients.length > 0" class="ingredients-list">
          <li v-for="(ingredient, index) in formattedIngredients" :key="`ingredient-${id}-${index}`">
            {{ ingredient }}
          </li>
        </ul>
        <p v-else>Keine Zutaten angegeben.</p>
      </div>

      <div class="recipe-section">
        <h3>Anleitung:</h3>

        <p class="instructions" v-html="formattedInstructions"></p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.recipe-card {
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  margin-bottom: 20px;
  padding: 0;
  background-color: #fff;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

.recipe-image {
  width: 100%;
  height: 200px;
  object-fit: cover;
}

.recipe-content {
  padding: 20px;
}

.recipe-name {
  margin-top: 0;
  color: #333;
  font-size: 1.8em;
}

.preparation-time {
  font-style: italic;
  color: #555;
  margin-bottom: 15px;
}

.recipe-section {
  margin-bottom: 15px;
}

.recipe-section h3 {
  color: #444;
  border-bottom: 1px solid #eee;
  padding-bottom: 5px;
  margin-bottom: 10px;
  font-size: 1.2em;
}

.ingredients-list {
  list-style-type: disc;
  padding-left: 20px;
  margin: 0;
}

.ingredients-list li {
  margin-bottom: 5px;
  color: #666;
}

.instructions {
  line-height: 1.6;
  color: #666;
  white-space: pre-line;
}

</style>

<script setup>
import { ref, computed, onMounted } from "vue";

const medias = ref([]);
const chargement = ref(true);
const filtreType = ref("tous");
const filtreNote = ref("tous");
// Nouvelle variable pour stocker la saisie de l'utilisateur
const rechercheTexte = ref("");

const chargerDonneesDrupal = async () => {
  try {
    const response = await fetch("/api/medias"); // Votre endpoint propre via le proxy Vite
    if (!response.ok) throw new Error("Erreur réseau");
    medias.value = await response.json();
  } catch (error) {
    console.error("Erreur de connexion à l'API Drupal :", error);
  } finally {
    chargement.value = false;
  }
};

// Moteur de filtrage combiné (Type + Note + Recherche textuelle Titre/Genre)
const mediasFiltres = computed(() => {
  return medias.value.filter((item) => {
    // 1. Filtrage par type (Livre/Film)
    const matchType =
      filtreType.value === "tous" || item.type === filtreType.value;

    // 2. Filtrage par note minimale
    let matchNote = true;
    if (filtreNote.value !== "tous") {
      const noteMedia = parseInt(item.field_note) || 0;
      matchNote = noteMedia >= parseInt(filtreNote.value);
    }

    // 3. Filtrage par recherche textuelle (Titre ou Genre)
    let matchRecherche = true;
    if (rechercheTexte.value.trim() !== "") {
      const terme = rechercheTexte.value.toLowerCase();
      const titreLower = (item.title || "").toLowerCase();
      const genreLower = (item.field_genre || "").toLowerCase();

      // Vérifie si le terme recherché est inclus dans le titre OU dans le genre
      matchRecherche = titreLower.includes(terme) || genreLower.includes(terme);
    }

    return matchType && matchNote && matchRecherche;
  });
});

onMounted(() => {
  chargerDonneesDrupal();
});
</script>

<template>
  <div class="container mx-auto px-4 py-10 font-sans text-gray-900 min-h-screen bg-gray-50">
    <header class="mb-12 text-center">
      <span class="text-xs font-bold uppercase tracking-widest text-blue-600 bg-blue-50 px-3 py-1 rounded-full border border-blue-200">
        Architecture NPM / Vite Découplée (Proxy actif)
      </span>
      <h1 class="text-4xl font-black text-gray-800 mt-3 tracking-tight">Mon Portail d'Avis Médias</h1>
      <p class="text-md text-gray-500 mt-2">API Rest Drupal 10 (Docker) + App Vue 3 (Vite)</p>
    </header>

    <!-- Panneau de Contrôle Enrichi -->
    <div class="bg-white p-6 rounded-2xl shadow-xs border border-gray-200 mb-8 flex flex-col gap-6">
      
      <!-- Ligne 1 : Barre de Recherche Textuelle -->
      <div class="w-full">
        <label class="block text-xs font-bold text-gray-400 uppercase mb-2">Recherche globale</label>
        <div class="relative">
          <input 
            v-model="rechercheTexte" 
            type="text" 
            placeholder="Rechercher par titre (ex: Parasite) ou catégorie (ex: Science-fiction)..." 
            class="w-full border border-gray-300 rounded-xl p-3 pl-10 bg-gray-50 text-sm font-medium focus:ring-2 focus:ring-blue-500 focus:bg-white transition outline-none"
          />
          <!-- Petit indicateur visuel de recherche -->
          <span class="absolute left-3 top-3.5 text-gray-400">🔍</span>
          <!-- Bouton pour effacer rapidement la recherche -->
          <button 
            v-if="rechercheTexte" 
            @click="rechercheTexte = ''" 
            class="absolute right-3 top-2.5 text-xs bg-gray-200 hover:bg-gray-300 text-gray-600 px-2 py-1 rounded-md transition"
          >
            Effacer
          </button>
        </div>
      </div>

      <!-- Ligne 2 : Filtres de sélection & Compteur -->
      <div class="flex flex-wrap gap-6 justify-between items-center border-t border-gray-100 pt-4">
        <div class="flex flex-wrap gap-6 items-center">
          <div>
            <label class="block text-xs font-bold text-gray-400 uppercase mb-2">Filtrer par type</label>
            <select v-model="filtreType" class="border border-gray-300 rounded-xl p-3 bg-gray-50 text-sm font-medium focus:ring-2 focus:ring-blue-500 transition cursor-pointer">
              <option value="tous">Tous les types</option>
              <option value="Livre">Livres</option>
              <option value="Film">Films</option>
            </select>
          </div>

          <div>
            <label class="block text-xs font-bold text-gray-400 uppercase mb-2">Note minimale</label>
            <select v-model="filtreNote" class="border border-gray-300 rounded-xl p-3 bg-gray-50 text-sm font-medium focus:ring-2 focus:ring-blue-500 transition cursor-pointer">
              <option value="tous">Toutes les notes</option>
              <option value="5">⭐⭐⭐⭐⭐ (5/5)</option>
              <option value="4">⭐⭐⭐⭐ (4/5 ou +)</option>
              <option value="3">⭐⭐⭐ (3/5 ou +)</option>
            </select>
          </div>
        </div>
        
        <div class="text-sm font-bold text-blue-700 bg-blue-50 border border-blue-100 px-5 py-3 rounded-xl">
          🚀 {{ mediasFiltres.length }} élément(s) trouvé(s)
        </div>
      </div>

    </div>

    <!-- Le reste de votre code de loader et de grille de cartes (v-if / v-else) reste strictement inchangé -->
    <!-- ... -->



    <div
      v-if="chargement"
      class="text-center py-20 bg-white rounded-2xl border border-gray-200 shadow-xs"
    >
      <div
        class="inline-block animate-spin rounded-full h-10 w-10 border-4 border-blue-600 border-t-transparent mb-4"
      ></div>
      <p class="text-gray-500 font-medium">Connexion HTTP à l'API Drupal...</p>
    </div>

    <div
      v-else-if="mediasFiltres.length > 0"
      class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6"
    >
      <div
        v-for="media in mediasFiltres"
        :key="media.title"
        class="bg-white rounded-2xl shadow-xs border border-gray-200 overflow-hidden transform hover:-translate-y-2 transition duration-300 flex flex-col justify-between"
      >
        <div class="h-72 bg-gray-100 relative overflow-hidden group">
          <img
            :src="'http://localhost:8085' + media.field_image_de_couverture"
            :alt="media.title"
            class="w-full h-full object-cover transition duration-500 group-hover:scale-110"
          />
          <span
            class="absolute top-3 left-3 bg-gray-900 text-white text-[10px] font-black tracking-widest px-2.5 py-1 rounded-md uppercase"
          >
            {{ media.type }}
          </span>
        </div>
        <div class="p-5 flex-grow flex flex-col justify-between">
          <div>
            <span
              class="text-xs bg-blue-100 text-blue-800 px-2.5 py-1 rounded-full font-bold uppercase tracking-wider inline-block mb-2"
            >
              {{ media.field_genre || "Général" }}
            </span>
            <h3
              class="font-extrabold text-lg text-gray-800 leading-tight mb-2"
              v-html="media.title"
            ></h3>
          </div>
          <div
            class="flex justify-between items-center pt-4 border-t border-gray-100 mt-4"
          >
            <span class="text-sm text-gray-600 font-bold">
              {{
                media.field_note
                  ? "⭐ " + media.field_note + " / 5"
                  : "❌ Aucune note"
              }}
            </span>
          </div>
        </div>
      </div>
    </div>

    <div
      v-else
      class="text-center py-20 bg-white rounded-2xl border border-gray-200 shadow-xs"
    >
      <p class="text-gray-400 text-lg font-bold">
        Aucun média ne correspond aux critères. 🧐
      </p>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { ref, onMounted, computed } from "vue";

const characters = ref([]);
const page = ref(1);

const loadPage = async (pageNumber) => {
    const perPage = 8;
    if (pageNumber >= 1) {
        const response = await axios.get(`https://stranger-things-api.fly.dev/api/v1/characters?perPage=${perPage}&page=${pageNumber}`);

        // Check if characters are available before updating the state
        if (response.data.length > 0) {
            characters.value = response.data;
            page.value = pageNumber;

            if (response.data.length < perPage && pageNumber > 1) {
                const remainingCharacters = response.data.length;
                const nextResponse = await axios.get(`https://stranger-things-api.fly.dev/api/v1/characters?perPage=${perPage - remainingCharacters}&page=${pageNumber + 1}`);
                characters.value = characters.value.concat(nextResponse.data);
            }
        } else {
            // If no characters, return to the first page
            loadPage(1);
        }
    }
};

onMounted(() => {
    loadPage(page.value);
});

const filteredCharacters = computed(() => {
    return characters.value.filter(character => {
        return character.name || character.status || character.born || character.gender;
        // Add more conditions as needed
    });
});
</script>

<template>
    <div>
        <h1 class="text-4xl font-extrabold mb-8 text-center text-gray-800">Stranger Things Characters</h1>
        <div v-if="characters" class="flex justify-center items-center">
            <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-8">
                <div v-for="character in filteredCharacters" :key="character._id"
                    class="bg-white p-4 rounded-md shadow-md transition-transform hover:shadow-lg hover:scale-105">
                    <!-- Display character information here -->
                    <div class="flex justify-center items-center mb-2">
                        <img :src="character.photo" alt="Character Image" class="w-24 h-24 object-cover rounded-full" />
                    </div>
                    <p class="text-base font-semibold mb-2 text-center">{{ character.name }}</p>
                    <div class="text-sm text-gray-600">
                        <p v-if="character.status"><strong>Status:</strong> {{ character.status }}</p>
                        <p v-if="character.born"><strong>Born:</strong> {{ character.born }}</p>
                        <p v-if="character.gender"><strong>Gender:</strong> {{ character.gender }}</p>
                        <!-- Add more details as needed -->
                    </div>
                </div>
            </div>
        </div>
        <div v-else>
            <p class="text-xl text-center text-gray-600">No characters available. Returning to the first page...</p>
            <!-- Add a loading spinner or other visual indicator if desired -->
        </div>
        <div class="mt-12 flex justify-center items-center space-x-4">
            <button @click="loadPage(page - 1)" :disabled="page === 1"
                class="bg-blue-500 text-white px-6 py-3 rounded-md hover:bg-blue-600 transition">Previous</button>
            <span class="text-lg font-semibold">{{ page }}</span>
            <button @click="loadPage(page + 1)"
                class="bg-blue-500 text-white px-6 py-3 rounded-md hover:bg-blue-600 transition">Next</button>
        </div>
    </div>
</template>

  


<style scoped></style>
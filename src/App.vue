<script setup>
import Cards from './components/Cards.vue';


</script>

<template>
  <main>

    <Suspense>
      <template #default>
        <Cards />
      </template>
      <template #fallback>
        <div>
          <p>loding...</p>
        </div>
      </template>
    </Suspense>
  </main>
</template>

<style scoped></style>

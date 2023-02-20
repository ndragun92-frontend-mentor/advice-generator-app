<template>
  <slot />
  <footer
    class="fixed bottom-2 left-2 z-10 text-primary-lightCyan text-sm text-center md:text-left opacity-75 hover:opacity-100"
  >
    <div class="p-3">
      Challenge by
      <a
        class="text-primary-neonGreen hover:underline"
        href="https://www.frontendmentor.io?ref=challenge"
        target="_blank"
        >Frontend Mentor</a
      >
      & Coded by
      <a
        class="text-primary-neonGreen hover:underline"
        href="https://ndragun92.github.io/portfolio/"
        target="_blank"
        >Nemanja Dragun</a
      >
    </div>
  </footer>
</template>

<script lang="ts">
export default {
  name: "LayoutDefault",
};
</script>

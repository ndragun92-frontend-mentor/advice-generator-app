<template>
  <main class="h-full flex items-center justify-center p-[16px] sm:p-0">
    <div
      class="bg-neutral-darkGrayishBlue max-w-[540px] pt-[39px] sm:pt-[46px] pb-[64px] sm:pb-[72px] px-6 sm:px-12 text-center rounded-xl relative"
    >
      <h1
        class="text-primary-neonGreen uppercase tracking-[2.8px] sm:tracking-[3.5px] text-[12px] sm:text-[14px]"
      >
        <template v-if="pending">Loading...</template>
        <template v-else>
          Advice <span v-text="`#${result?.slip?.id}`" />
        </template>
      </h1>
      <blockquote
        class="mt-[22px] text-[24px] sm:text-[28px] leading-[33px] sm:leading-[38px] tracking-[-0.3px]"
        v-text="pending ? 'Loading...' : `“${result?.slip?.advice}”`"
      />
      <div class="mt-6 sm:mt-10">
        <img
          class="block sm:hidden mx-auto"
          src="/images/pattern-divider-mobile.svg"
          alt="Mobile divider"
        />
        <img
          class="hidden sm:block mx-auto"
          src="/images/pattern-divider-desktop.svg"
          alt="Desktop divider"
        />
      </div>
      <div
        class="absolute -bottom-8 left-0 right-0 flex items-center justify-center"
      >
        <button
          aria-label="Generate advice"
          class="w-16 h-16 bg-primary-neonGreen flex items-center justify-center rounded-full"
          :class="{ 'pointer-events-none': generating }"
          type="button"
          @click="onGenerateNew"
        >
          <img
            class="dice block"
            :class="{ 'animate-spin': generating }"
            src="/images/icon-dice.svg"
            alt="Icon dice"
          />
        </button>
      </div>
    </div>
  </main>
</template>

<script lang="ts">
export default {
  name: "PageHomepage",
};
</script>

<script lang="ts" setup>
const {
  pending,
  data: result,
  refresh,
} = await useFetch("https://api.adviceslip.com/advice", {
  transform: (res: any) => {
    if (!res) return {};
    return JSON.parse(res) as { id: number; advice: string };
  },
});

const generating = ref(false);
const onGenerateNew = () => {
  generating.value = true;
  console.log("generate new");
};
</script>

<style lang="scss" scoped>
button {
  &:not(.pointer-events-none) {
    @apply duration-200;
    img {
      @apply duration-200;
    }
    &:hover {
      @apply shadow-primary;
      .dice {
        @apply rotate-[135deg];
      }
    }
  }
  &.pointer-events-none {
    @apply shadow-primary;
  }
}
</style>

<template>
  <main class="h-full flex items-center justify-center p-[16px] sm:p-0">
    <div
      class="bg-neutral-darkGrayishBlue max-w-[540px] pt-[39px] sm:pt-[46px] pb-[64px] sm:pb-[72px] px-6 sm:px-12 text-center rounded-xl relative"
    >
      <client-only>
        <h1
          class="text-primary-neonGreen uppercase tracking-[2.8px] sm:tracking-[3.5px] text-[12px] sm:text-[14px]"
        >
          Advice <span v-text="`#${advice?.id || 0}`" />
        </h1>
        <blockquote
          id="quote"
          class="mt-[22px] text-[24px] sm:text-[28px] leading-[33px] sm:leading-[38px] tracking-[-0.3px]"
        />
      </client-only>
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
  <transition-fade>
    <div v-if="globalLoading" class="fixed top-0 right-0 bottom-0 left-0">
      <section
        class="bg-neutral-darkBlue relative place-items-center grid h-screen w-screen gap-4"
      >
        <div
          class="bg-primary-neonGreen w-48 h-48 absolute animate-ping rounded-full delay-5s shadow-xl"
        />
        <div
          class="bg-primary-neonGreen w-32 h-32 absolute animate-ping rounded-full shadow-xl"
        />
        <div
          class="bg-primary-neonGreen w-24 h-24 absolute animate-pulse rounded-full shadow-xl"
        />
        <img
          class="dice block animate-spin"
          src="/images/icon-dice.svg"
          alt="Icon dice loading"
        />
      </section>
    </div>
  </transition-fade>
</template>

<script lang="ts">
export default {
  name: "PageHomepage",
};
</script>

<script lang="ts" setup>
import TypeIt from "typeit";
import { promiseTimeout } from "@vueuse/core";

const advice = reactive({
  id: "",
  text: "",
});

const element = ref<any>(null);
const onGet = async () => {
  const response = await $fetch<string>("https://api.adviceslip.com/advice");
  if (response) {
    const formattedData = JSON.parse(response);
    advice.id = formattedData?.slip?.id;
    advice.text = formattedData?.slip?.advice;
  }
};
await onGet();

const globalLoading = ref(true);
onMounted(async () => {
  await nextTick(() => onTypeAnimation(advice.text));
  await promiseTimeout(1000);
  globalLoading.value = false;
});

const onTypeAnimation = (text) => {
  if (process.server) return;
  if (!element.value) {
    element.value = new TypeIt("#quote", {
      speed: 50,
    });
  }
  element.value
    .delete()
    .type(`“${text}”`)
    .exec(async () => {
      await promiseTimeout(50);
      generating.value = false;
    })
    .flush()
    .go();
};

const generating = ref(true);
const onGenerateNew = async () => {
  generating.value = true;
  await onGet();
  element.value?.reset();
  await nextTick(() => onTypeAnimation(advice.text));
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

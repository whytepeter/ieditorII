<template>
  <div class="flex items-center gap-2 border p-2">
    <span>Width</span>

    <input
      @input="updateSize('width')"
      type="number"
      class="w-12 h-7 border focus:outline-none text-[#a670ff] text-center"
      v-model="properties.width"
    />
  </div>

  <div class="flex items-center gap-2 border p-2">
    <span>Height</span>

    <input
      @input="updateSize('height')"
      type="number"
      class="w-12 h-7 border focus:outline-none text-[#a670ff] text-center"
      v-model="properties.height"
    />
  </div>
</template>

<script setup lang="ts">
import {
  onMounted,
  computed,
  reactive,
  ref,
  watch,
  Ref,
  ComputedRef,
} from "vue";

const props = defineProps<{
  element: HTMLElement | null;
}>();

const data: Ref<HTMLElement | null> = ref(null);

const properties: Record<string, number | null> = reactive({
  width: 10,
  height: null,
});

const watchElement: ComputedRef<HTMLElement | null> = computed(
  () => props.element
);

watch(watchElement, () => {
  if (props.element) {
    setProperty();
  }
});

onMounted(() => {
  setProperty();
});

const setProperty = (): void => {
  if (!props.element) return;
  data.value = props.element;

  properties.height = parseInt(data.value.style.height, 10);
  properties.width = parseInt(data.value.style.width, 10);
};

const updateSize = (type: any) => {
  if (!data.value) return;
  data.value.style[type] = (properties as any)[type] + "px";
  data.value.style.maxWidth = "98%";
  data.value.style.maxHeight = "98%";
};
</script>

<style></style>

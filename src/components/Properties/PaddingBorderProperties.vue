<template>
  <div class="flex items-center gap-2 border p-2">
    <span class="cursor-pointer" @click="toggle.padding = !toggle.border"
      >Border</span
    >

    <input
      v-if="toggle.border"
      @input="udpateBorder('borderWidth')"
      type="number"
      class="w-10 h-7 border focus:outline-none text-[#a670ff] text-center"
      v-model="properties.borderWidth"
    />
    <input
      v-if="toggle.border"
      @input="udpateBorder('borderColor')"
      class="w-8 h-7 hover:border-[#a670ff]"
      type="color"
      id="colorPicker"
      v-model="properties.borderColor"
    />
    <span
      v-if="toggle.border"
      class="cursor-pointer"
      @click="toggle.border = !toggle.border"
      >Rounded</span
    >
    <input
      v-if="toggle.border"
      @input="udpateBorder('borderRadius')"
      type="number"
      class="w-10 h-7 border focus:outline-none text-[#a670ff] text-center"
      v-model="properties.borderRadius"
    />
    <i
      @click="toggle.border = !toggle.border"
      class="text-lg text-gray-700 cursor-pointer"
      :class="[toggle.border ? 'pi pi-angle-left' : 'pi pi-angle-right']"
    ></i>
  </div>

  <div class="flex items-center gap-2 border p-2">
    <span class="cursor-pointer" @click="toggle.padding = !toggle.padding"
      >Padding</span
    >

    <input
      v-if="toggle.padding"
      @input="updatePadding('paddingY', $event)"
      type="number"
      class="w-10 h-7 border focus:outline-none text-[#a670ff] text-center"
      v-model="properties.paddingTop"
    />
    <input
      v-if="toggle.padding"
      @input="updatePadding('paddingX', $event)"
      type="number"
      class="w-10 h-7 border focus:outline-none text-[#a670ff] text-center"
      v-model="properties.paddingLeft"
    />

    <i
      @click="toggle.padding = !toggle.padding"
      class="text-lg text-gray-700 cursor-pointer"
      :class="[toggle.padding ? 'pi pi-angle-left' : 'pi pi-angle-right']"
    ></i>
  </div>
</template>

<script setup lang="ts">
import { onMounted, computed, reactive, ref, Ref, watch } from "vue";

const props = defineProps<{
  element: HTMLElement | null;
}>();

const toggle: Record<string, boolean> = reactive({
  border: false,
  padding: false,
});

const data: Ref<HTMLElement | null> = ref(null);

const properties: Record<string, string | number | null> = reactive({
  borderColor: "",
  borderWidth: null,
  borderRadius: null,
  paddingTop: null,
  paddingLeft: null,
});

const watchElement = computed(() => props.element);

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

  properties.borderColor = data.value.style.borderColor;
  properties.borderWidth = parseInt(data.value.style.borderWidth, 10);
  properties.borderRadius = parseInt(data.value.style.borderRadius, 10);
  properties.paddingTop = parseInt(data.value.style.paddingTop, 10);
  properties.paddingLeft = parseInt(data.value.style.borderWidth, 10);
};

const udpateBorder = (type: any): void => {
  if (!data.value) return;
  const value = (properties as any)[type];
  data.value.style[type] = type == "borderColor" ? value : value + "px";
};

const updatePadding = (type: "paddingX" | "paddingY", e: any): void => {
  if (!data.value) return;
  const value = e.target.value;

  if (type == "paddingX") {
    properties.paddingLeft = Number(value);
    data.value.style.padding = `${properties.paddingTop}px ${properties.paddingLeft}px`;
  } else {
    properties.paddingTop = Number(value);
    data.value.style.padding = `${properties.paddingTop}px ${properties.paddingLeft}px`;
  }
};
</script>

<style></style>

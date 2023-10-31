<template>
  <div
    ref="editor"
    class="h-screen pt-10 md:ml-72 w-full flex flex-col gap-4 relative"
  >
    <Properties :element="activeElement" />

    <div ref="container" id="container">
      <div
        @dragover.prevent
        @mousemove="trackmouse"
        @click="selectElement"
        @drop="onDrop"
        ref="layout"
        id="layout"
        style="background-color: antiquewhite"
        class="relative overflow-hidden mx-auto w-[450px] h-[450px]"
      ></div>
    </div>
  </div>
</template>

<script setup lang="ts">
import Properties from "./Properties/Index.vue";
import { ref, onMounted, Ref } from "vue";
import { Element } from "@/typings/types";

const draggingElement: Ref<HTMLElement | null> = ref(null);
const activeElement: Ref<HTMLElement | null> = ref(null);
const layout: Ref<HTMLElement | null> = ref(null);
const container: Ref<HTMLElement | null> = ref(null);
const leftCorrection: Ref<number> = ref(0);
const topCorrection: Ref<number> = ref(0);

const defaultTemplates = ref<string>(
  `<div id="layout" class="relative overflow-hidden mx-auto w-[450px] h-[450px] selected" style="background: rgb(243, 143, 112); border-radius: 1000px;"><div draggable="true" type="Shape" style="left: 14.3403px; top: 13.3646px; background: rgb(243, 144, 111); width: 420px; height: 420px; position: absolute; max-width: 98%; max-height: 98%; border-radius: 10000px; border-color: rgb(255, 255, 255); border-width: 3px; z-index: 1;" class="selected"></div><i class="pi pi-star-fill" draggable="true" type="icon" style="left: 205.007px; top: 34.3698px; font-size: 30px; color: rgb(210, 124, 96); position: absolute; z-index: 1;"></i><i class="pi pi-star-fill" draggable="true" type="icon" style="left: 147.007px; top: 47.3646px; font-size: 26px; color: rgb(210, 124, 96); position: absolute; z-index: 1;"></i><i class="pi pi-star-fill" draggable="true" type="icon" style="left: 264.007px; top: 49.3646px; font-size: 26px; color: rgb(210, 124, 96); position: absolute; z-index: 1;"></i><h4 draggable="true" type="Heading" style="left: 75.0069px; top: 88.3646px; font-size: 25px; font-weight: bold; color: rgb(255, 255, 255); max-width: 98%; line-height: 1.15; position: absolute; text-align: center; width: 300px; max-height: 98%; font-family: Arial, sans-serif; z-index: 1;" class="">All the text and elements in this popup should be editable and draggable</h4><input placeholder="E-mail" draggable="true" type="Fields" style="left: 54.0069px; top: 198.365px; padding: 10px 20px; color: rgb(21, 0, 55); outline: none; border-radius: 12px; font-size: 21px; position: absolute; width: 350px; max-width: 98%; max-height: 98%; z-index: 1; font-family: Arial, sans-serif;" class=""><button draggable="true" type="Button" style="left: 59.0069px; top: 271.365px; padding: 10px 20px; color: rgb(255, 255, 255); background: rgb(84, 84, 84); border-radius: 11px; font-size: 22px; position: absolute; text-align: center; font-weight: bold; width: 332px; max-width: 98%; max-height: 98%; font-family: Arial, sans-serif; z-index: 1;" class="">SIGN UP NOW</button><p draggable="true" type="Text" style="left: 111.007px; top: 339.365px; font-size: 14px; color: rgb(255, 255, 255); max-width: 100%; position: absolute; font-family: Arial, sans-serif; z-index: 1;" class="">No credit card required. No Suprises</p></div>`
);

const onDrop = (e: DragEvent): void => {
  const layout = document.getElementById("layout");

  const res: Element =
    e.dataTransfer && JSON.parse(e.dataTransfer.getData("element"));
  const { isNew, element, icon, type } = res;

  const data: HTMLElement = isNew
    ? document.createElement(element)
    : draggingElement.value!; //The element that's being dragged in properties

  // Position the element relative the mouse position on the layout
  const layoutRect = layout && layout.getBoundingClientRect();
  const rectLeft = layoutRect?.left || 0;
  const rectTop = layoutRect?.top || 0;
  const offsetX = e.clientX - rectLeft - leftCorrection.value;
  const offsetY = e.clientY - rectTop - topCorrection.value;

  data.style.left = offsetX + "px";
  data.style.top = offsetY + "px";

  if (isNew) {
    addDefaultStyles(data, type);
    data.style.position = "absolute";

    if (icon) {
      data.classList.add("pi", icon);
    }

    data.setAttribute("draggable", "true");
    data.setAttribute("type", type);
    data.addEventListener("dragstart", startDrag);
    data.addEventListener("click", selectElement);

    layout && layout.appendChild(data);

    activeElement.value = data;
  }
};

const addDefaultStyles = (el: HTMLElement, type: string): void => {
  const defaultStyles: Record<string, any> = {
    Button: {
      padding: "10px 20px",
      color: "#ffff",
      background: "#A670FF",
      borderRadius: "7px",
      fontSize: "16px",
      fontFamily: "Arial, sans-serif",
      zIndex: 1,
    },
    Fields: {
      padding: "10px 20px",
      color: "#150037",
      outline: "none",
      borderRadius: "7px",
      fontSize: "16px",
      fontFamily: "Arial, sans-serif",
      zIndex: 1,
    },
    Text: {
      fontSize: "16px",
      fontFamily: "Arial, sans-serif",
      color: "#ffff",
      maxWidth: "100%",
      background: null,
      zIndex: 1,
    },
    Heading: {
      fontSize: "30px",
      fontFamily: "Arial, sans-serif",
      fontWeight: "Bold",
      color: "#ffff",
      maxWidth: "100%",
      background: null,
      lineHeight: "1.5",
      zIndex: 1,
    },
    Icon: {
      fontSize: "30px",
      color: "#ffff",
      zIndex: 1,
    },
    Shape: {
      background: "#ffff",
      width: "100px",
      height: "100px",
      zIndex: 1,
    },
  };

  const styles = defaultStyles[type];
  if (styles) {
    for (const prop in styles) {
      el.style.setProperty(prop, styles[prop]);
    }
  }

  if (type !== "Shape" && type !== "Icon") {
    el.innerText = type;
  }
};

const selectElement = (e: Event & any): void => {
  if (activeElement.value) {
    activeElement.value.classList.remove("selected");
  }

  activeElement.value = e.target;
  activeElement.value && activeElement.value.classList.add("selected");
};

const startDrag = (e: Event & any): void => {
  draggingElement.value = e.target;
  const data: Element = {
    type: e.target.getAttribute("type"),
    isNew: false,
    element: "",
    icon: null,
  };

  e.dataTransfer.setData("element", JSON.stringify(data));
};

const trackmouse = (e: Event & any): void => {
  if (e.target.id !== "layout") {
    leftCorrection.value = e.offsetX;
    topCorrection.value = e.offsetY;
  }
};

const setTemplate = (): void => {
  const saved = localStorage.getItem("saved");
  const template = saved ? JSON.parse(saved) : null;

  if (!container.value) return;
  container.value.innerHTML = template
    ? template.element
    : defaultTemplates.value;
  addStartDragEvent();
};

const addStartDragEvent = (): void => {
  const layout = document.getElementById("layout");

  //Add the necessary events to the layout when setting the default templates from local storage
  if (!layout) return;
  layout.addEventListener("mousemove", trackmouse);
  layout.addEventListener("drop", onDrop);
  layout.addEventListener("click", selectElement);

  layout.addEventListener("dragover", (e) => {
    e.preventDefault();
  });

  //Add the dragstar event to all elements on the layout
  const childElements = layout.querySelectorAll("*");
  childElements &&
    childElements.forEach((element) => {
      element.addEventListener("dragstart", startDrag);
    });
};

onMounted(() => {
  setTemplate();
});
</script>

<style scope>
.selected {
  border: 2px solid #a670ff;
}
</style>

<template>
  <div class="flex items-center gap-2">
    <NavBar />
    <SideBar />
    <Editor />
  </div>
</template>

<script setup lang="ts">
import NavBar from "@/components/NavBar.vue";
import SideBar from "@/components/SideBar.vue";
import Editor from "@/components/Editor.vue";
</script>

<style scoped></style>

<template>
    <div v-if="visible" class="modal">
      <div class="modal-content">
        <span class="close" @click="closeModal">&times;</span>
        <h2>{{ title }}</h2>
        <input v-model="inputLabel" placeholder="Enter label" />
        <button @click="submit">Submit</button>
      </div>
    </div>
  </template>
  
  <script setup lang="ts">
  import { defineProps, defineEmits, ref } from 'vue';
  
  const props = defineProps({
    visible: Boolean,
    title: String,
  });
  
  const emits = defineEmits(['close', 'submit']);
  const inputLabel = ref('');
  
  function closeModal() {
    emits('close');
  }
  
  function submit() {
    emits('submit', inputLabel.value);
    inputLabel.value = '';
  }
  </script>
  
  <style scoped>
  .modal {
    display: flex;
    position: fixed;
    z-index: 1;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    overflow: auto;
    background-color: rgba(0,0,0,0.4);
  }
  
  .modal-content {
    margin: auto;
    padding: 20px;
    border: 1px solid #888;
    width: 80%;
    max-width: 500px;
    background-color: white;
  }
  
  .close {
    color: #aaa;
    float: right;
    font-size: 28px;
    font-weight: bold;
  }
  
  .close:hover,
  .close:focus {
    color: black;
    text-decoration: none;
    cursor: pointer;
  }
  
  input {
    display: block;
    width: 100%;
    padding: 10px;
    margin: 10px 0;
    box-sizing: border-box;
  }
  
  button {
    padding: 10px 20px;
    background-color: #4CAF50;
    color: white;
    border: none;
    cursor: pointer;
  }
  
  button:hover {
    background-color: #45a049;
  }
  </style>
  
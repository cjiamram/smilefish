<!-- src/components/Modal.vue -->
<template>
    <div class="modal" v-if="show">
      <div class="modal-content">
        <slot></slot>
        <button @click="closeModal" class="btn btn-danger">Close</button>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      show: Boolean,
    },
    methods: {
      closeModal() {
        this.$emit('close');
      },
    },
  };
  </script>
  
  <style scoped>
  .modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    align-items: center;
    justify-content: center;
  }
  
  .modal-content {
    background-color: #111C44;
    padding: 20px;
    border-radius: 10px;
    min-width: 400px;
    width:50%;
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.3);
    align-items: center;
    justify-content: center;
  }
  </style>
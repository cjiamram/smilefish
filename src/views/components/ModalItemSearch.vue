<!-- src/components/Modal.vue -->
<template>
    <div class="modal" v-if="show">
      <div class="modal-content">
        <slot></slot>
                <div class="col-lg-12">
                  <label for="example-text-input" class="form-control-label">คำค้น</label>
                  <input name="keyword" id="keyword" v-model="keyword" class="form-control" type="text"  />
                </div>
     
        <hr/>
        <div class="row">
       
                <div class="col-lg-12">
                <a @click="close2Parent" class="btn btn-danger">ปิด</a>&nbsp;
                <a @click="searchtoParent" class="btn btn-primary">แสดงผล</a>
                </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {

    props: {
      show: Boolean,
      searchItem:Function,
      closeModal:Function  
    },
    data(){
            return{
                 keyword:''
            }
    },
    methods: {
       close2Parent() {
         this.closeModal();
       },

       searchtoParent(){
            const keyword=this.keyword!==""?this.keyword:"%";
            this.searchItem(keyword);
            this.closeModal();
       }
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
    min-width: 280px;
    width:30%;
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.3);
    align-items: center;
    justify-content: center;
  }
  </style>
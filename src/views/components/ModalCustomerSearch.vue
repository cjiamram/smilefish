<!-- src/components/Modal.vue -->
<template>
    <div class="modal" v-if="show">
      <div class="modal-content">
        <slot></slot>
        <div class="row">
                <div class="col-md-12">
                  <label for="example-text-input" class="form-control-label"
                    >คำค้น</label
                  >
                  <input name="keyword" id="keyword" v-model="element.keyword" class="form-control" type="text"  />
                </div>
                <div class="col-lg-12">
                  <label for="example-text-input" class="form-control-label"
                    >จังหวัด</label
                  >
                  <select  class="form-control" v-model="element.province_search" >
                        <option value="">****เลือกจังหวัด****</option>
                        <option v-for="province in provinces" :key="province.id" :value="province.province_code">
                        {{ province.province_name }}
                        </option>
                    </select>
                </div>
              

                   
        </div>
        <hr/>
        <div class="row">
                <div class="col-md-12">
                <a @click="closetoParent" class="btn btn-danger">ปิด</a>&nbsp;
                <a @click="searchtoParent" class="btn btn-primary">แสดงผล</a>&nbsp;
                </div>
        </div>

       
      </div>
    </div>
  </template>
  
  <script>
  export default {

    props: {
      show: Boolean,
      provinces:Array,
      searchCustomer:Function,
      closeModal:Function 
    },
    data(){
            return{
                  element:{
                    keyword:'',
                    province_search:''
                }

            }
    },
    methods: {
      closetoParent() {
        this.closeModal();
      },

       searchtoParent(){
            //alert(this.element.keyword);
            this.searchCustomer(this.element.keyword,this.element.province_search);
           // this.$emit('close');
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
    min-width: 400px;
    width:30%;
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.3);
    align-items: center;
    justify-content: center;
  }
  </style>
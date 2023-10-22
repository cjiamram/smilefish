<template>
<div class="card">
    <div class="card-header pb-0">
      <h6>ลูกค้า</h6>
    </div>
    <div class="card-body px-0 pt-0 pb-2">
      <div class="table-responsive p-0">
        <table class="table align-items-center mb-0">
            <thead>
            <tr>
              <th style="width:50px">#</th>
              <th class="text-uppercase text-secondary text-lg font-weight-bolder opacity-7">ชื่อ-สกุล</th>
              <th
                class="text-uppercase text-secondary text-lg font-weight-bolder opacity-7 ps-2"
              >ติดต่อ</th>
              <th
                class="text-center text-uppercase text-secondary text-lg font-weight-bolder opacity-7"
              >จังหวัด</th>
            </tr>
          </thead>
          <tbody v-for="(customer,index) in this.customerArray" :key="customer.id">
            <tr >
            <!-- Render customer data here -->
            <td style="text-align:center">{{ index + 1 }}</td>
            <td><h6 class="mb-0 text-sm">{{ customer.customer_name }}</h6>
             
            <div class="py-1 pb-1">
              <button class="btn btn-info" @click="readtoParent(customer.id)">
                <i class="fas fa-edit"></i>
              </button>&nbsp;
              <button class="btn btn-danger" @click="deletetoParent(customer.id)">
                <i class="fas fa-trash-alt"></i>
              </button>
            </div>
            </td>
            <td>
              <ContactCard :TelNo="customer.telno" :Line="customer.line" :FaceBook="customer.facebook" :Email="customer.email" :Farm="customer.farm"/>

            </td>
            <td>{{ customer.province_name }}</td>
            <!-- Add more table cells for other customer properties -->
            </tr>

        </tbody>
        <tbody>
          <tr><td colspan="4"> 
          
            <div style="display: flex; justify-content: space-between; align-items: center; width: 330px">
              <a class="btn btn-primary btn-outline-primary btn-round" @click="movePrevious">
                  <i class="fa fa-angle-double-left" style="font-size:24px"></i>
              </a>

              <a class="btn btn-info btn-outline-primary btn-round" @click="moveBack">
                  <i class="fa fa-angle-left" style="font-size:24px"></i>
              </a>
             

              <label class="form-control" style="background-color: rgb(161, 205, 244); color: red; text-align: center;"><h6 class="mb-0 text-sm">{{ this.pageIndex+1 }} of {{this.pageCount  }}</h6></label>
           

              <a class="btn btn-info btn-outline-primary btn-round" @click="moveNext">
                  <i class="fa fa-angle-right" style="font-size:24px"></i>
              </a>

              <a class="btn btn-primary btn-outline-primary btn-round" @click="moveLast">
                  <i class="fa fa-angle-double-right" style="font-size:24px"></i>
              </a>
          </div>
          </td></tr>

        </tbody>
    
        </table>
        <div>
  </div>
      </div>
    </div>   
</div>
<div>
</div>
<div></div>
</template>

<script>

import ContactCard from './ContactCard.vue';

export default {

  props: {
    customerList: Array, 
    readData: Function,
    deleteData:Function,
    itemperPage:Number
  },
  
  data(){
        return {
              customerArray:[],
              currentIndex:0,
              pageCount:0,
              pageIndex:0,
              pageActive:false 
        }
    },
  mounted() {
      this.setPageNo(); 
      this.customerArray=this.seekCustomers();
  },

  updated() {
    this.setPageNo(); 
    this.customerArray=this.seekCustomers();

  },

  components:{
    ContactCard,
  },
  
  methods:{
 


    setPageNo(){
        const custLength=this.customerList.length;
        this.pageCount=(Math.ceil(custLength/this.itemperPage)) ; 
        // if(custLength%this.itemperPage>0){
        //   this.pageCount+=1;
        // }
        this.pageActive= this.pageCount>1?true:false;
        this.pageIndex=0;
    },

    seekCustomers(){
      const startIndex = this.pageIndex * this.itemperPage;
      const endIndex = startIndex + this.itemperPage;
      const custArr=this.customerList.slice(startIndex,endIndex);
      return custArr;
    },
    
    readtoParent(id){
          this.readData(id);
     },

    deletetoParent(id){
        this.deleteData(id);

     },
    movePrevious(){
        this.pageIndex=0;
        this.customerArray=this.seekCustomers();
      },
    moveLast(){
        this.pageIndex=this.pageCount>0?this.pageCount-1:0
        this.customerArray=this.seekCustomers();
      },
    moveNext(){
        if(this.pageIndex<<this.pageCount){
            this.pageIndex++;
            this.customerArray=this.seekCustomers();
        }        
      },
    moveBack(){
        if(this.pageIndex>0){
            this.pageIndex--;
            this.customerArray=this.seekCustomers();
        }
      }
    },

}
</script>

<style>

</style>
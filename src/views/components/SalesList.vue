<template>
  
 <div class="card" >
    <div class="card-header pb-0" style="background-color:cornflowerblue">
      <h6>รายการขาย</h6>
    </div>
    <div class="card-body px-0 pt-0 pb-2" >
        <div class="table-responsive p-0">
            <table class="table align-items-center mb-0" >
            <thead>
            <tr>
              <th style="width:50px;text-align:center">#</th>
              <th class="text-uppercase text-secondary text-lg font-weight-bolder opacity-7">สินค้า</th>
              <th
                class="text-uppercase text-secondary text-lg font-weight-bolder opacity-7 ps-2">จำนวน</th>
              <th
                class="text-center text-uppercase text-secondary text-lg font-weight-bolder opacity-7">ราคา</th>
                <th 
                class="text-center text-uppercase text-secondary text-lg font-weight-bolder opacity-7">เป็นเงิน</th>
              
             
            </tr>
          </thead>
          <tbody v-for="(product,index) in this.sales" :key="product.id">
            <tr>
                <td style="text-align:center">{{ index+1 }}</td>
                <td >
                  <div>
                   {{ product.product_name }}&nbsp; <button @click="delete2parent(product.index)" class="btn btn-danger">
                        <i class="fa fa-trash" aria-hidden="true"></i>
                    </button>
                  </div>
                </td>
                <td style="text-align:center" >{{ this.formatwithComma(product.quantity) }}</td>
                <td style="text-align:center">{{ this.formatwithComma(product.price) }}</td>
                <td style="text-align:center">{{ this.formatwithComma(product.price*product.quantity) }}</td>
            
            </tr>
           
          </tbody>
          <tbody>
            <tr><td colspan="4" style="text-align: right;">Total</td><td style="text-align:center">{{ this.formatwithComma(this.total) }}</td></tr>
          </tbody>
          </table>
        </div>
    </div>

</div>
</template>

<script>
export default {
   
    data(){
        return {
          total:0
        }
    },
    
    props:{
        sales:Array,
        deleteItemSale:Function 
    },
    mounted(){
        this.setTotal();
    },
    methods:{
      setTotal(){

        this.total=0;
        this.sales.forEach((element) => {
                        //product.price*product.quantity
                         this.total+=element.price*element.quantity;
        });
        
      },
      
      convert2Array(objects){
            const resultArray = Object.entries(objects).map(([key, value]) => {
                    return { key: parseInt(key), value };
            });
            return resultArray;
      },

      delete2parent(index){
        this.deleteItemSale(index);
        this.setTotal();
      },

      formatwithComma(number){
                const options = {
                style: 'decimal', // or 'currency', 'percent', etc.
                maximumFractionDigits: 2, // Sets the maximum number of fraction digits
                // Add more options as needed
                };
                const formattedNumber = number.toLocaleString('en-US', options);
                return formattedNumber;
        },
    }
}
</script>

<style>

</style>
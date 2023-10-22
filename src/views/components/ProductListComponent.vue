<template>
      <div class="card">
        <div class="card-header pb-0">
            <h6>สินค้า</h6>
        </div>
        <div class="card-body px-0 pt-0 pb-2">
            <div class="table-responsive p-0">
            <table class="table align-items-center mb-0">
            <thead>
            <tr>
              <th style="width:50px;text-align:center">#</th>
              <th class="text-uppercase text-secondary text-lg font-weight-bolder opacity-7">สินค้า</th>
              <th
                class="text-uppercase text-secondary text-lg font-weight-bolder opacity-7 ps-2">ราคา/หน่วย</th>
                <th
                class="text-uppercase text-secondary text-lg font-weight-bolder opacity-7 ps-2">จำนวน</th>
            </tr>
          </thead>
          <tbody v-for="(product,index) in this.products" :key="product.id">
            <tr>
              <td style="text-align:center">{{ index+1 }}</td>
              <td>{{ product.product }}
                <div class="py-1 pb-1 pl-1">
                         <button class="btn btn-info" @click="read2Parent(product.id)" title="แก้ไข">
                            <i class="fas fa-edit"></i>
                        </button>&nbsp;
                        <button class="btn btn-danger" @click="delete2Parent(product.id)" title="ลบ">
                            <i class="fas fa-trash-alt"></i>
                        </button>&nbsp;
                        <button class="btn btn-warning"  title="ขายสินค้า">
                            <i class="ni ni-cart"></i>
                        </button>&nbsp;
                       
                       
                </div>
              </td>
              <td>{{ this.formatwithComma(product.price) }}</td>
              <td>{{ this.formatwithComma(product.quantity) }}</td>
              <td></td>

            </tr>

          </tbody>
          </table>
        </div>
        </div>
      </div>
  
</template>

<script>
export default {
    props:{
      products:[],
      readProduct:Function,
      deleteProduct:Function
    },
    methods:{

      
      formatwithComma(number){
                const options = {
                style: 'decimal', // or 'currency', 'percent', etc.
                maximumFractionDigits: 2, // Sets the maximum number of fraction digits
                // Add more options as needed
                };

               // console.log(number);

                const formattedNumber = number.toLocaleString('en-US', options);
                return formattedNumber;
        },

      read2Parent(id){
          this.readProduct(id);
      },
      delete2Parent(id){
          this.deleteProduct(id);
      }

    }



}
</script>

<style>

</style>
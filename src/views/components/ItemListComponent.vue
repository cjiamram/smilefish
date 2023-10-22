<template>
  <div class="card">
    <div class="card-header pb-0">
      <h6>วัสดุ</h6>
    </div>
    <div class="card-body px-0 pt-0 pb-2">
      <div class="table-responsive p-0">
        <table class="table align-items-center mb-0">
            <thead>
            <tr>
              <th style="width:50px;text-align:center">#</th>
              <th class="text-uppercase text-secondary text-lg font-weight-bolder opacity-7">วัสดุ</th>
              <th
                class="text-uppercase text-secondary text-lg font-weight-bolder opacity-7 ps-2">จำนวน-หน่วย</th>
              <th
                class="text-center text-uppercase text-secondary text-lg font-weight-bolder opacity-7">ต้นทุน(เฉลี่ย)/หน่วย</th>
                <th
                class="text-center text-uppercase text-secondary text-lg font-weight-bolder opacity-7">เป็นเงิน</th>
             
            </tr>
          </thead>
          <tbody v-for="(item,index) in this.items" :key="item.id">
            <tr>
                <td style="text-align:center">{{ index+1 }}</td>
                <td width="40%">{{ item.item_name }}
               
                <div class="py-1 pb-1 pl-1">
                         <button class="btn btn-info" @click="readtoParent(item.id)" title="แก้ไข">
                            <i class="fas fa-edit"></i>
                        </button>&nbsp;
                        <button class="btn btn-danger" @click="deletetoParent(item.id)" title="ลบ">
                            <i class="fas fa-trash-alt"></i>
                        </button>&nbsp;
                        <button class="btn btn-warning" @click="settoDepositParent(item.id)"  title="เบิกวัสดุ">
                          <i class="ni ni-money-coins" ></i>
                        </button>&nbsp;
                        <button class="btn btn-success" @click="settoReceiveParent(item.id)"  title="รับวัสดุ">
                          <i class="ni ni-basket" ></i>
                        </button>
                </div>
                </td>
                <td width="90px" style="text-align:center">{{  this.formatwithComma(item.quantity) }} {{ item.unit }}</td>
                <td width="90px" style="text-align:center">{{  this.formatwithComma(item.cost) }}</td>
                <td width="90px" style="text-align:center">{{  this.formatwithComma(item.cost*item.quantity)  }} B</td>
                

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
        items:Array,
        readItem:Function,
        deleteItem:Function,
        showDepositModal:Function,
        showReceiveModal:Function 

    },
    methods:{
        readtoParent(id){
            this.readItem(id);
        },
        deletetoParent(id){
            this.deleteItem(id);
        },
        settoDepositParent(id){
          this.showDepositModal(id);
        },

        settoReceiveParent(id){
          this.showReceiveModal(id);
        },

        

        formatwithComma(number){
                const options = {
                style: 'decimal', // or 'currency', 'percent', etc.
                maximumFractionDigits: 2, // Sets the maximum number of fraction digits
                // Add more options as needed
                };

               // console.log(number);

                const formattedNumber = number.toLocaleString('en-US', options);
                return formattedNumber;
        }
    }
}
</script>

<style>

</style>
<template>
  <div class="modal" v-if="show">
        <div class="modal-content">
            <slot>

            </slot>
            <div class="row">
                <div class="col-md-12">
                  <label for="example-text-input" class="form-control-label"
                    >วัสดุ</label>
                  <input type="hidden" class="form-control"  >
                  <label class="form-control"  style="color: black; background-color: green;">{{ this.itemSendReceive.item_name }}</label>
                </div>
                <div class="col-md-6">
                  <label for="example-text-input" class="form-control-label"
                    >จำนวนที่รับเข้า</label>
                  <input name="quantity" id="quantity" v-model="this.entry.quantity" class="form-control" type="number"  />
                </div>
                <div class="col-md-6">
                  <label for="example-text-input" class="form-control-label"
                    >ราคา/หน่วย</label>
                  <input name="price" id="price" v-model="this.entry.price" class="form-control" type="number"  />
                </div>
                <div class="col-md-12">
                  <label for="example-text-input" class="form-control-label"
                    >ผู้จำหน่าย</label>
                  <input name="supplier" id="supplier" v-model="this.entry.supplier" class="form-control" type="text"  />
                </div>
                <div class="col-md-12">
                  <label for="example-text-input" class="form-control-label"
                    >วันที่รับเข้า</label>
                  <input name="receive_date" id="receive_date" v-model="this.entry.receive_date" class="form-control" type="date"  />
                </div>
            </div>
            <hr/>
            <div class="row">
                <div class="col-md-12">
                <a class="btn btn-danger" @click="closetoParent">ปิด</a>&nbsp;
                <a  class="btn btn-success" @click="receive_item_trans">รับเข้า</a>&nbsp;
            </div>
        </div>

        </div>
    </div>
</template>

<script>
import config from '../../config/config.json';
import Swal from 'sweetalert2';

export default {
    data(){
        return {
            entry:{
                item_code: '',
                quantity: 0,
                price: 0,
                supplier: '',
                receive_date:  'Y-m-dTH:i:S.Z'
            }
        }
    },
  
    props:{
        show:Boolean,
        queryItem:Function,
        readItembyItemCode:Function,

        itemSendReceive:{
          type: Object,
           default: () => ({})
        },
        closeReceiveModal:Function
      

    },
 

    methods:{
        // getItem(){
        //     console.log(this.itemSendReceive);
        // },
        
        getCurrentDate() {
            const today = new Date();
            const year = today.getFullYear();
            const month = String(today.getMonth() + 1).padStart(2, '0');  // Months are zero-indexed
            const day = String(today.getDate()).padStart(2, '0');
            return `${year}-${month}-${day}`;
        },
        closetoParent(){
            this.closeReceiveModal();
        },

        async receive_item_trans(){
           //***************************************** */
           try{
                 const apiUrl=config.apiUrl; 
                 const url = `${apiUrl}entry/receive_item_trans/`;
               
                 const method= 'POST';
                 const headers = {
                     'Content-Type': 'application/json',
                 };
                 this.entry.receive_date=  this.entry.receive_date+"T00:00:00.0000"

                 //console.log( this.entry);
 
                 const requestOptions = {
                     method,
                     headers,
                     body: JSON.stringify(this.entry),
                 };

                 const response = await fetch(url, requestOptions);
                
                 if (!response.ok) {
                         throw new Error('Network response was not ok');
                 }
                 
                 let data = await response.json();
                 console.log(data);
                 if(data.Flag==true){
                        this.queryItem("%");
                        this.showSuccess();
                        this.closeReceiveModal();
                        this.readItembyItemCode(this.entry.item_code);

                 }  
             } 
             catch (error) {
                       console.error('Error posting data:', error);
             }
         },
         showSuccess() {
            Swal.fire({
                    title: 'Success!',
                    text: 'การบันทึกข้อมูลเสร็จสมบูรณ์แล้ว',
                    icon: 'success',
            });
        }
    },
    watch:{

        itemSendReceive(itemDep){
          this.entry={
                item_code:itemDep.item_code,
                quantity:0,
                price:0,
                supplier:'',
                receive_date:this.getCurrentDate()
            }
        }

    }

}
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
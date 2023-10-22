<template>
      <div class="modal" v-if="show">
        <div class="modal-content">
            <slot>

            </slot>
            <div class="row">
                <div class="col-md-12">
                  <label for="example-text-input" class="form-control-label"
                    >วัสดุ</label>
                  <input type="hidden" class="form-control" v-model="this.deposit.item_code" >
                  <label class="form-control"  style="color: black; background-color: green;">{{ this.itemSendDeposit.item_name }}</label>
                </div>
                <div class="col-md-12">
                  <label for="example-text-input" class="form-control-label"
                    >จำนวนที่เบิก</label>
                  <input name="quantity" id="quantity" v-model="this.deposit.quantity" class="form-control" type="number"  />
                </div>
                <div class="col-md-12">
                  <label for="example-text-input" class="form-control-label"
                    >วันที่</label>
                  <input name="created_at" id="created_at" v-model="this.deposit.created_at" class="form-control" type="date"  />
                </div>
            </div>
            <hr/>
            <div class="row">
                <div class="col-md-12">
                <a class="btn btn-danger" @click="closetoParent">ปิด</a>&nbsp;
                <a  class="btn btn-success" @click="add_deposit_item">เบิก</a>&nbsp;
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
        return{
           deposit:{
            item_code:'',
            item_name:'',
            quantity:0,
            created_at: 'Y-m-dTH:i:S.Z'
           }
        }
    },
    props:{
        show: Boolean,
        closeDepositModal:Function,
        queryItem:Function,
        readItembyItemCode:Function,
        itemSendDeposit:{
          type: Object,
           default: () => ({})
        },
      
        
    },
    methods:{
          async add_deposit_item(){
           
          if(this.deposit.quantity<=0){
            this.showLessQuantity();
            return ;
          }

          //***************************************** */
          try{
                const apiUrl=config.apiUrl; 
                const url = `${apiUrl}deposit/add_deposit_item/`;
              
                const method= 'POST';
                const headers = {
                    'Content-Type': 'application/json',
                };
                this.deposit.created_at=  this.deposit.created_at+"T00:00:00.0000"

                const requestOptions = {
                    method,
                    headers,
                    body: JSON.stringify(this.deposit),
                };
                //console.log(JSON.stringify(this.deposit));
                const response = await fetch(url, requestOptions);
                if (!response.ok) {
                        throw new Error('Network response was not ok');
                }
                
                let data = await response.json();
                //console.log(data);
                if(data.Flag==true){
                        this.closeDepositModal();
                        this.queryItem("%");
                        this.showSuccess();
                        this.readItembyItemCode(this.deposit.item_code);
                }  
            } 
            catch (error) {
                      console.error('Error posting data:', error);
            }
        
        },
        closetoParent() {
            this.closeDepositModal();
        },
        showLessQuantity() {
            Swal.fire({
                    title: 'Error!',
                    text: 'กรุณาระบุจำนวนที่ต้องการเบิก',
                    icon: 'error',
            });
        },
        showSuccess() {
            Swal.fire({
                    title: 'Success!',
                    text: 'การบันทึกข้อมูลเสร็จสมบูรณ์แล้ว',
                    icon: 'success',
            });
        },
        getCurrentDate() {
            const today = new Date();
            const year = today.getFullYear();
            const month = String(today.getMonth() + 1).padStart(2, '0');  // Months are zero-indexed
            const day = String(today.getDate()).padStart(2, '0');
            return `${year}-${month}-${day}`;
        }
    },
  
    watch: {
        itemSendDeposit(itemDep){
          this.deposit={
                item_code:itemDep.item_code,
                item_name:itemDep.item_name,
                quantity:0,
                created_at:this.getCurrentDate()
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
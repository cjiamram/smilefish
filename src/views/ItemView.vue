<template>
      <div class="py-4 container-fluid" >
        <div class="item-info" id="item-info">
                <h6>ข้อมูลวัสดุ</h6>
                <a href="#" class="badge bg-gradient-primary" ><i class="fa fa-refresh" aria-hidden="true" @click="clearItem">&nbsp;เคลียร์</i></a>&nbsp;
                <div v-if="this.showToggle">
                    <a href="#" class="badge bg-gradient-warning" @click="toggleInput">Hide
                    </a>
                </div>
                <div v-else>
                    <a href="#" class="badge bg-gradient-success" @click="toggleInput">Show
                    </a>
                </div>
            </div>

        <div class="row" v-show="isMainVisible">
                <div class="col-lg-5 mb-lg">
                    <form @submit.prevent="postData">
                    <div class="card z-index-2">

                        <div class="card-body" style="height: 452px;">
                        <div class="row">
                           
                            <div class="col-md-12">
                                    <label for="item_name"  class="form-control-label">วัสดุ:</label>
                                    <input type="hidden" v-model="this.item.id">
                                    <input type="hidden" v-model="this.item.item_code">
                                    <input type="text" id="item_name" class="form-control" v-model="item.item_name" required />
                            </div>
                            <div class="col-md-6">
                                    <label for="quantity"  class="form-control-label">จำนวน:</label>
                                    <input type="number" id="quantity" class="form-control" readonly  v-model="item.quantity" />
                            </div>   
                            <div class="col-md-6"> 
                                <label for="unit"  class="form-control-label">หน่วย:</label>
                                    <select v-model="item.unit" class="form-control" >
                                            <option value="">****หน่วย****</option>
                                            <option v-for="unit in units" :key="unit.id" :value="unit.code">
                                            {{ unit.unit }}
                                            </option>
                                    </select>
                                    
                             </div>  

                            <div class="col-md-6">    
                                     <label for="cost"  class="form-control-label">ต้นทุน/หน่อย:</label>
                                    <input type="number" id="cost" class="form-control" v-model="item.cost" />
                           </div>
                           <div class="col-md-6">&nbsp;  
                            </div>
                           <div class="col-md-12">
                            <hr/>
                            </div> 
                           <div class="col-md-12">
                                 <button type="submit" class="btn btn-success">บันทึก</button>&nbsp;
                                 <a class="btn btn-info" href="#" @click="showModal">ค้นหา</a>
                            </div>
                        </div>
                        </div>
                    </div>
                    </form>
                </div>
                <div class="col-lg-7 mb-lg">
                    <div class="card z-index-2">
                      <ChartItemView  :datasets="datasets" ref="chartComponent" ></ChartItemView>
                    </div>
                </div>
        </div>
        <br/>
        <div>
            <ItemList :items="this.items" :readItem="readItem"  :deleteItem="deleteItem"  :showDepositModal="showDepositModal" :showReceiveModal="showReceiveModal" />
        </div>
    </div>

    
  <Modal :show="isModalVisible" :closeModal="closeModal"  :searchItem="queryItem" >
      <!-- Modal content goes here -->
      <h2>ค้นหาข้อมูล</h2>
      <!-- <p>This is a custom modal component.</p> -->
    </Modal>


    <ModalDeposit :show="isModalDepositVisible" :queryItem="queryItem"  :itemSendDeposit="itemSendDeposit" :closeDepositModal="closeDepositModal" :readItembyItemCode="readItembyItemCode" >
      <!-- Modal content goes here -->
      <h2>เบิกวัสดุ</h2>
      <!-- <p>This is a custom modal component.</p> -->
    </ModalDeposit>

    <ModalReceive :show="isModalReceiveVisible" :queryItem="queryItem"  :itemSendReceive="itemSendReceive" :closeReceiveModal="closeReceiveModal" :readItembyItemCode="readItembyItemCode">
      <!-- Modal content goes here -->
      <h2>รับวัสดุ</h2>
      <!-- <p>This is a custom modal component.</p> -->
    </ModalReceive>
</template>

<script>
import config from '../config/config.json';
import Swal from 'sweetalert2';
import ItemList from './components/ItemListComponent.vue';
import Modal from './components/ModalItemSearch.vue';
import ModalDeposit from './components/ModalDeposit.vue';
import ModalReceive from './components/ModalReceiveItem.vue';
import ChartItemView from './components/ChartItem.vue';

export default {
    data(){
        return {
            item:{
                id:0,
                item_code:'',
                item_name:'',
                quantity:0,
                cost:0,
                status:0,
                unit:''
            },

            itemSendDeposit:{
                item_code:'',
                item_name:'',
                cost:0
            } , 

         
            itemSendReceive:{
                item_code:'',
                item_name:'',
                cost:0
            } , 

            items:[],
            isMainVisible:true,
            showToggle:true,
            units:[],
            isModalVisible:false,
            isModalDepositVisible:false ,
            isModalReceiveVisible:false,
            passIndex:0,
            datasets:{
                labels:[],
                data_entries:[],
                data_deposits:[],
                }
           
        }
    },

    components:{
        ItemList,
        Modal,
        ModalDeposit,
        ModalReceive ,
        ChartItemView
    },

    mounted(){
        this.listUnit();
        this.queryItem("%");
    },

    methods:{
        
         retreiveTransaction(){
            const apiUrl=config.apiUrl;
                const url=apiUrl+"report/report_subtotal_item/"+this.item.item_code;
                let entries=[];
                let deposits=[];
                let labels=[];
                 fetch(url)
                    .then(response => response.json())
                    .then(data => {

                        data.forEach(item => {
                                labels.push(item.trans_date);
                                entries.push(item.entry_sub_total);
                                deposits.push(item.deposit_sub_total);
                         });
                       this.datasets.data_entries=entries;
                       this.datasets.data_deposits=deposits;
                       this.datasets.labels=labels;
                       this.$refs.chartComponent.renderChart(this.datasets);

                            
                    })
                    .catch(error => {
                        console.error('Error fetching data:', error);
                 });

        },
        
        async listUnit(){
                    const apiUrl=config.apiUrl;
                    const url=apiUrl+"item/get_unit/";
                    await fetch(url)
                    .then(response => response.json())
                    .then(data => {
                        this.units=data;
                    })
                    .catch(error => {
                        console.error('Error fetching data:', error);
                    });
        },


     

        async readItem(id){
                const apiUrl=config.apiUrl;
                const url=apiUrl+"item/"+id;
                this.showToggle=true;
                this.isMainVisible=true;
                await fetch(url)
                    .then(response => response.json())
                    .then(data => {
                        this.item=data;
                        this.retreiveTransaction();
                        document.getElementById("item-info").scrollIntoView({ behavior: 'smooth' })

                        
                    })
                    .catch(error => {
                        console.error('Error fetching data:', error);
                 });
        },

        async readItembyItemCode(itemCode){
                const apiUrl=config.apiUrl;
                const url=apiUrl+"item/get_data_by_itemcode/"+itemCode;
                this.isMainVisible=true;
                await fetch(url)
                    .then(response => response.json())
                    .then(data => {
                        this.item=data;
                        this.showToggle=true;
                        
                    })
                    .catch(error => {
                        console.error('Error fetching data:', error);
                 });
        },

        
        setDepositItem(id){
                const apiUrl=config.apiUrl;
                this.passIndex=id;
                const url=apiUrl+"item/"+id;
                 fetch(url)
                    .then(response => response.json())
                    .then(data => {

                        const dep={
                            item_code:data.item_code,
                            item_name:data.item_name,
                            cost:data.cost 
                        }
                       
                        this.itemSendDeposit=dep;

                    })
                    .catch(error => {
                        console.error('Error fetching data:', error);
                 });

        },

        setReceviceItem(id){
                const apiUrl=config.apiUrl;
                this.passIndex=id;
                const url=apiUrl+"item/"+id;
                 fetch(url)
                    .then(response => response.json())
                    .then(data => {

                        const receive={
                            item_code:data.item_code,
                            item_name:data.item_name,
                            cost:data.cost 
                        }
                       
                        this.itemSendReceive=receive;

                    })
                    .catch(error => {
                        console.error('Error fetching data:', error);
                 });

        },

        async deleteItem(id){
                

                this.isMainVisible=true;
                await Swal.fire({
                            title: 'Confirm Delete?',
                            text: 'ต้องการลบข้อมูลหรือไม่!',
                            icon: 'warning',
                            showCancelButton: true,
                            confirmButtonText: 'Yes, delete it!',
                            cancelButtonText: 'No, cancel',
                }).then((result) => {
                if (result.isConfirmed) {
                        const apiUrl=config.apiUrl;
                        fetch(apiUrl+'item/delete/'+id)
                        .then(response => response.json())
                        .then(data => {
                                  if(data.Flag===true){
                                    Swal.fire('Deleted!', 'Your item has been deleted.', 'success');
                                    
                                  }else{
                                    Swal.fire('Error!', data.message , 'Error');
                                  }  
                                  this.clearItem();
                                this.queryItem("%");
                            }
                        )
                        .catch(error => {
                            console.error('Error to delete:', error);
                        });
                       

                       
                } else if (result.isDenied) {
                    Swal.fire('Cancelled', 'Your item is safe :)', 'info');
                }
                });


        },

        async clearItem(){
                this.item={
                    id:0,
                    item_code:'',
                    item_name:'',
                    quantity:0,
                    cost:0,
                    status:0,
                    unit:''
                }
           },

        convert2Array(objects){
            const resultArray = Object.entries(objects).map(([key, value]) => {
                    return { key: parseInt(key), value };
            });
            return resultArray;
        },

        async queryItem(keyword){
            const apiUrl = config.apiUrl;
            const url = `${apiUrl}item/search_item/${keyword}`;

            await fetch(url)
            .then(response => response.json())
            .then(data => {
                //console.log(data);
                this.items=data;
                 if(data.length>0){
                     this.item = data[0];
                     this.retreiveTransaction();
                 }

            }).catch(error => {
                console.error('Error fetching data:', error);
            });
                
         },

         toggleInput(){
                this.showToggle=!this.showToggle;
                this.isMainVisible=!this.isMainVisible;

        },

        
        showModal() {
          this.isModalVisible = true;
        },
            
        showDepositModal(id) {
            this.isModalDepositVisible=true ;
            this.setDepositItem(id);

        },

             
        showReceiveModal(id) {
            this.isModalReceiveVisible=true ;
            this.setReceviceItem(id);
        },


        closeModal() {
          this.isModalVisible = false;
        },
        
        closeDepositModal() {
          this.isModalDepositVisible=false; 
        },
                
        closeReceiveModal() {
          this.isModalReceiveVisible=false; 
        },

        showSuccess() {
            Swal.fire({
                    title: 'Success!',
                    text: 'การบันทึกข้อมูลเสร็จสมบูรณ์แล้ว',
                    icon: 'success',
            });
        },

        async postData(){
            try{
                const apiUrl=config.apiUrl; 
                const url = this.item.id === 0? `${apiUrl}item/add/`: `${apiUrl}item/update/${ this.item.id}`;
                const method= this.item.id === 0?'POST':'PUT';
                const headers = {
                    'Content-Type': 'application/json',
                };

                const requestOptions = {
                    method,
                    headers,
                    body: JSON.stringify(this.item),
                };

                //console.log(JSON.stringify(this.item));

                const response = await fetch(url, requestOptions);
                if (!response.ok) {
                        throw new Error('Network response was not ok');
                }
                
                let data = await response.json();
                //console.log(data);
                if(method==="POST"){
                    this.item.item_code=data.item_code;     
                }

                if(data.Flag==true){
                        this.clearItem();
                        this.showSuccess();
                        this.queryItem("%");
                }  
            } 
            catch (error) {
                      console.error('Error posting data:', error);
            }
        } 

    }
}
</script>

<style scoped>
        .item-info {
          display: inline-flex;
          align-items: center; /* จัดให้เนื้อหาตรงกลางตามแนวแกนแนวตั้ง */
        }

        .item-info h6 {
          margin-right: 10px; /* กำหนดระยะห่างด้านขวาของ <h6> จากปุ่ม */
        }
</style>
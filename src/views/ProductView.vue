<template>
    <div class="py-4 container-fluid">
        <div class="product-info" id="product-info">
                <h6>ข้อมูล</h6>
                <a href="#" class="badge bg-gradient-primary" @click="clearProduct"><i class="fa fa-refresh" aria-hidden="true">&nbsp;เคลียร์</i></a>&nbsp;
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
                <div class="card-body">
                    <div class="row">
                        <div class="col-md-12">
                                    <label for="product_name"  class="form-control-label">ปลา:</label>
                                    <input type="hidden" v-model="this.product.id">
                                    <input type="hidden" v-model="this.product.product_code">
                                    <input type="text" id="product_name" class="form-control" v-model="product.product" required />
                        </div>
                        <div class="col-md-6">
                            <label for="price"  class="form-control-label">ราคา:</label>
                            <input id="price" class="form-control" v-model="product.price" type="number" />
                        </div>
                        <div class="col-md-6">
                            <label for="product_type"  class="form-control-label">ประเภทสินค้า:</label>
                            <select id="product_type" class="form-control" v-model="product.product_type">
                                <option value="">****ประเภทสินค้า****</option>
                                    <option v-for="productype in productTypes" :key="productype.id" :value="productype.type_code">
                                        {{ productype.description }}
                                    </option>

                            </select>
                        </div>
                        <div class="col-md-6">
                            <label for="quantity"  class="form-control-label">จำนวน:</label>
                            <input id="quantity" class="form-control" :value="this.formatwithComma(this.product.quantity)" readonly type="text" />
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
                
            </div>
        </div>
        <br/>
        <div>
            <ProductList :products="this.products" :readProduct="readProduct" :deleteProduct="deleteProduct" />
        </div>

    </div>
</template>

<script>
import config from '../config/config.json';
import ProductList from './components/ProductListComponent.vue';

export default {
    components:{
        ProductList
    },
    data(){
     
        return{
            product:{
                id:0,
                product_code:'',
                product:'',
                quantity:0,
                price:0,
                product_type:''
            },
            products:[],
            productTypes:[],
            isMainVisible:true,
            showToggle:false 
        } 
    },
    mounted(){
        this.fetchProductTypes();
        this.searchProduct("%");
    }
    ,
    methods:{
        async clearProduct(){
                this.product={
                    id:0,
                    product_code:'',
                    product:'',
                    price:0,
                    product_type:''
                }
        },

        async searchProduct(keyword){
            const apiUrl=config.apiUrl;
            keyword=keyword===""?"%":keyword;
            await fetch(apiUrl+'product/search/'+keyword)
            .then(response => response.json())
            .then(data => {
                    this.products=data;
                   // console.log(data);
            })
            .catch(error => {
                    console.error('Error fetching data:', error);
            });
        },

        async deleteProduct(id){
            const apiUrl=config.apiUrl;
            await fetch(apiUrl+'product/delete/'+id)
            .then(response => response.json())
            .then(data => {
                 return data.Flag;
            })
            .catch(error => {
                console.error('Error fetching data:', error);
            });
        },

        async readProduct(id){
            const apiUrl=config.apiUrl;
            await fetch(apiUrl+'product/'+id)
            .then(response => response.json())
            .then(data => {
                 this.product=data;
            })
            .catch(error => {
                console.error('Error fetching data:', error);
            });
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
        },

      

        fetchProductTypes(){
            const apiUrl=config.apiUrl;
            fetch(apiUrl+'productiontype/list_product_types/')
            .then(response => response.json())
            .then(data => {
                //console.log(data);
                this.productTypes=data;
            })
            .catch(error => {
                console.error('Error fetching data:', error);
            });

        },


        async postData(){

            try{
                const apiUrl=config.apiUrl; 
                const url = this.item.id === 0? `${apiUrl}product/add/`: `${apiUrl}product/update/${ this.product.id}`;
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
                if(method==="POST"){
                    this.item.item_code=data.item_code;     
                }

                if(data.Flag==true){
                        this.clearProduct();
                        // this.showSuccess();
                        this.searchProduct("%");
                }  
            } 
            catch (error) {
                      console.error('Error posting data:', error);
            }

        },
        
        toggleInput(){
                this.showToggle=!this.showToggle;
                this.isMainVisible=!this.isMainVisible;

        },
    },


  

}
</script>

<style scoped>
        .product-info {
          display: inline-flex;
          align-items: center; /* จัดให้เนื้อหาตรงกลางตามแนวแกนแนวตั้ง */
        }

        .product-info h6 {
          margin-right: 10px; /* กำหนดระยะห่างด้านขวาของ <h6> จากปุ่ม */
        }
</style>
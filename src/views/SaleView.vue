<template>
    <div class="py-4 container-fluid" >
            <div class="item-info" id="item-info"  >
                <h6>ขายสินค้า</h6>
                <a href="#" class="badge bg-gradient-primary" ><i class="fa fa-refresh" aria-hidden="true" @click="clearSale">&nbsp;เคลียร์</i></a>&nbsp;
                <div v-if="this.showToggle">
                    <a href="#" class="badge bg-gradient-warning" @click="toggleInput">Hide
                    </a>
                </div>
                <!-- <div v-else>
                    <a href="#" class="badge bg-gradient-success" @click="toggleInput">Show
                    </a>
                </div> -->
            </div>
            <div class="row" v-show="isMainVisible">
                <div class="col-lg-5 mb-lg">
                    <!-- <form @submit.prevent="postData"> -->
                            <div class="card z-index-2">
                                <div class="card-body">
                                <div class="row"> 
                             
                            <div class="col-md-12 mb-lg">
                                    <label for="Customer"  class="form-control-label">ลูกค้า:</label>
                                     <div class="autocomplete-container">
                                        <input type="hidden" v-model="this.sale.customer_code">
                                        <input
                                            type="text" class="form-control"
                                            v-model="this.customerInput"
                                            @input="handleCustomers"
                                            @click="displayCustomer"
                                            
                                            placeholder="[คำค้น]"
                                            />
                                            <ul v-if="showSuggestions"  class="suggestions-panel">
                                                <li v-for="(customer,index) in this.filteredCustomers" :key="index" @click="handleItemClick(customer)">
                                                    {{ customer.customer_name }}
                                                </li>
                                            </ul>
                                            <div class="close-icon" @click="closeAutocomplete"  v-if="showSuggestions"><span>&times;</span></div>  
                                </div>   
                            </div>
                              

                            <div class="col-md-6 mb-lg">
                                    <label for="Product"  class="form-control-label">Product:</label>
                                    <select class="form-control" v-model="this.sale.product_code" @change="onProductChange" >
                                        <option value="">****เลือกสินค้า****</option>
                                        <option v-for="product in products" :key="product.id" :value="product.product_code">
                                                        {{ product.product }}
                                        </option>
                                    </select>
                            </div>
                    
                        <div class="col-md-6 mb-lg">
                            <label for="price"  class="form-control-label">ราคา:</label>
                            <input type="number" v-model="this.sale.price" class="form-control" id="price">
                        </div>
                    
                        <div class="col-md-6 mb-lg">
                                <label for="quantity"  class="form-control-label">จำนวน:</label>
                                <input type="number" class="form-control" v-model="this.sale.quantity" id="quantity">
                        </div>
                        <div class="col-md-6 mb-lg">
                            <label for="total"  class="form-control-label">เป็นเงิน:</label>
                            <input type="text" :value="this.formatwithComma(this.sale.quantity*this.sale.price)" readonly class="form-control" id="total">
                        </div >
                        <div class="col-md-12 mb-lg">&nbsp;</div>
                    
                        <br>
                        <div class="col-md-12">
                       
                                <button type="submit" @click="appendSales"  class="btn btn-primary">เพิ่ม</button>&nbsp;
        
                                <button type="submit" v-show="this.isSave" @click="saveSales"  class="btn btn-success">บันทึก</button>


                        </div> 
                        </div>
                    </div>
                    </div>
                <!-- </form> -->
                </div>
                <div class="col-lg-7 mb-lg">
                    <SalesList :sales="sales"  :deleteItemSale="deleteItemSale" ref="refreshRef"/>
                  
                </div>
            </div>
        <br/>
        <div class="row">
            
            <div class="col-lg-6 mb-lg">
                <ChartPieSale   :title="this.title"  ref="refRenderChart" />
            </div>
            <div class="col-lg-6 mb-lg">
                <ChartLineSale :title="this.titleLine" ref="refSaleComponent"/>
            </div>
            
        </div>

           
    </div>
  
</template>

<script>
import Swal from 'sweetalert2'
import config from '../config/config.json';
import SalesList from './components/SalesList.vue';
import ChartPieSale from './components/ChartPieSale.vue';
import ChartLineSale from './components/ChartLineSale.vue';

export default {
    components:{
        SalesList,
        ChartPieSale,
        ChartLineSale  

    },
    data(){
            return{
                sale:{
                    index:0,
                    product_code:'',
                    product_name:'',
                    customer_code:'',
                    quantity:0,
                    price:0
                },
                customer_code:'',
                products:[],
                stock:0,
                isMainVisible:true ,
                customers:[],
                showSuggestions: false,
                customerInput:'',
                filteredCustomers:[],
                sales:[] ,
                isSave:false,
                datasets:{
                    customer:'',
                    labels:[],
                    data:[],
                    colors:[]
                },
                
                datasetSales:{
                    customer:'',
                    labels:[],
                    sales:[],

                } 
              

            }
    },
    mounted(){
        this.getProduct("%");
        this.fetchCustomer();
    },
    methods:{

        createRandomColor() {
                const letters = '0123456789ABCDEF';
                let color = '#';
                for (let i = 0; i < 6; i++) {
                    color += letters[Math.floor(Math.random() * 16)];
                }
                return color;
        },


        salesTransaction(){
            const apiUrl=config.apiUrl;
                const url=apiUrl+"report/report_sub_total_sale/"+this.customer_code;
                let sales=[];
                let labels=[];
                 fetch(url)
                    .then(response => response.json())
                    .then(data => {

                        data.forEach(sale => {
                                labels.push(sale.trans_date);
                                sales.push(sale.sub_total);
                                
                         });
                       this.datasetSales.customer=this.customerInput;
                       this.datasetSales.sales=sales;
                       this.datasetSales.labels=labels;
                       this.$refs.refSaleComponent.renderChart(this.datasetSales);

                            
                    })
                    .catch(error => {
                        console.error('Error fetching data:', error);
                 });

        },

        

        async getSaleReport() {
                    if (this.customer_code !== "") {
                        const apiUrl = config.apiUrl;
                        const url = `${apiUrl}report/get_sale_pie_by_customer_code/${this.customer_code}`;

                        try {
                        const response = await fetch(url);

                        if (!response.ok) {
                            throw new Error('Network response was not ok');
                        }

                        const data = await response.json();
                       // console.log(data);

                        // Initialize datasets for each API call to avoid accumulation of data
                        let datasets = {
                            labels: [],
                            data: [],
                            colors: [],
                            customer: this.customerInput
                        };

                        // Process the data and update datasets
                        data.forEach(element => {
                            datasets.labels.push(element.product);
                            datasets.data.push(element.amount);
                            datasets.colors.push(this.createRandomColor());
                        });

                        this.datasets=datasets;
                        this.$refs.refRenderChart.createChart(datasets);
                      

                        } catch (error) {
                        console.error('Error fetching data:', error);
                        return null; // Return null if there's an error
                        }
                    } else {
                        return null; // Return null if customerCode is empty
                    }
        },

        handleItemClick(customer){
            this.customerInput=customer.customer_name;
            this.title="สัดส่วนการสั่งซื้อสินค้า "+customer.customer_name;
            this.titleLine="อัตราการสั่งซื้อสินค้า "+customer.customer_name;
            this.sale.customer_code=customer.customer_code;
            this.customer_code=customer.customer_code;
            this.showSuggestions = false;
            this.getSaleReport();
            this.salesTransaction();
        },

        closeAutocomplete() {
            // Hide the suggestions and reset the input
            this.showSuggestions = false;
            this.customerInput = '';
        },

        afterAppendSale(){
            this.sale={
                    index:0,
                    product_code:'',
                    product_name:'',
                    customer_code:this.customer_code,
                    quantity:0,
                    price:0
                }
        },

        clearSale(){
            this.sale={
                    index:0,
                    product_code:'',
                    product_name:'',
                    customer_code:'',
                    quantity:0,
                    price:0
                }
        },

        deleteItemSale(index){
           this.sales.splice(index, 1);
           this.setIndex();
        },
        
        setIndex(){
            for (let i = 0; i < this.sales.length; i++) {
                this.sales.index=i; 
            }

            this.isSave=this.sales.length>0?true:false;
        }, 

        updateSaleCode(){
            const apiUrl=config.apiUrl;
            const url=apiUrl+"sale/update_sale_code/";
            fetch(url)
            .then(response => response.json())
            .then(data => {
                return data.Flag;
            }).catch(error => {
                    console.error('Error fetching data:', error);
            });

        },

        async saveElement(saleitem) {
                    
                    try {
                        const sale = {
                                index: saleitem.index,
                                product_code: saleitem.product_code,
                                product_name: saleitem.product_name,
                                customer_code:saleitem.customer_code,
                                quantity: saleitem.quantity,
                                price: saleitem.price,
                        };


                        const apiUrl = config.apiUrl;
                        const method = 'POST';
                        const url = `${apiUrl}sale/sale_product/`;
                        const headers = {
                                'Content-Type': 'application/json'
                        };

                        const requestOptions = {
                                method,
                                headers,
                                body: JSON.stringify(sale)
                        };

                        const response = await fetch(url, requestOptions);

                        if (!response.ok) {
                                throw new Error('Network response was not ok');
                        }

                        const data = await response.json();
                        this.updateSaleCode();
                        return data.Flag;
                    } catch (error) {
                                console.error('Error saving element:', error);
                                throw error; // Re-throw the error to be handled by the caller if needed
                    }
        },

        async getSaleCode(){
            const apiUrl=config.apiUrl;
            const url=apiUrl+"sale/gen_code/";
            await fetch(url)
            .then(response => response.json())
            .then(data => {
                console.log(data.sale_code);
                return data.sale_code;
            }).catch(error => {
                    console.error('Error fetching data:', error);
            });


        },

        saveSales(){
                        
             if(this.sales.length>0){
                //******************************* */
                    let flag=true;
                     this.sales.forEach((element) => {
                            this.saveElement(element);
                           
                    });

                    this.updateSaleCode();

                    if(flag===true){
                        Swal.fire(
                                'Success!',
                                'การบันทึกข้อมูลเสร็จสมบูรณ์แล้ว',
                                'success'
                                );
                    }
                    this.sales=[];
                    this.clearSale();

                //****************************** */
             }
        },
        

       
        convert2Array(objects){
            const resultArray = Object.entries(objects).map(([key, value]) => {
                    return { key: parseInt(key), value };
            });
            return resultArray;
        },
        
        appendSales(){
            if(this.sale.quantity>0){
                    this.customer_code=this.sale.customer_code;
                    this.sales.push(this.sale);           
                    this.setIndex();
                    
                    this.afterAppendSale();
                    this.$refs.refreshRef.setTotal();
            }else{
                Swal.fire({
                    title: 'Error!',
                    text: 'กรุณาระบุจำนวนสินค้า',
                    icon: 'error',
                });
            }
            console.log(this.sales);
        },


        handleCustomers() {
            const customerInputLower = this.customerInput.toLowerCase();

             this.filteredCustomers = this.customers.filter(
                 //customer => customer.customer_name.toLowerCase().includes(customerInputLower)
                 customer => customer.customer_name.includes(customerInputLower)
             );
            this.showSuggestions = this.filteredCustomers.length > 0;
        },
        displayCustomer(){
            this.filteredCustomers=this.customers;
            this.showSuggestions = this.customers.length > 0;
        },
        async fetchCustomer(){
                    
                        const apiUrl=config.apiUrl;
                        const url=apiUrl+"customer/list_customer/";
                        await fetch(url)
                                .then(response => response.json())
                                .then(data => {
                                    this.customers=data;
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

        async onProductChange(){ 
            const apiUrl=config.apiUrl;
            const url=apiUrl+"product/get_price_by_productcode/"+this.sale.product_code;
            await fetch(url)
                    .then(response => response.json())
                    .then(data => {
                       
                        this.sale.price=data.price;
                        this.sale.quantity=0;
                        this.sale.product_code=data.product_code;
                        this.sale.product_name=data.product_name;
                    })
                    .catch(error => {
                        console.error('Error fetching data:', error);
                 });

        },

        async getProduct(keyword){
            
            const apiUrl=config.apiUrl;
            const url=apiUrl+"product/search/"+keyword;
            await fetch(url)
                    .then(response => response.json())
                    .then(data => {
                        this.products=data;
                    })
                    .catch(error => {
                        console.error('Error fetching data:', error);
                 });
        }
    },
    watch:{
            product_code(newVal){
                console.log('Watcher - Selected fruit changed:', newVal);  
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

        .autocomplete-container {
            position: relative;
        }

        .close-icon {
                position: absolute;
                top: 50px;
                right: 10px;
                cursor: pointer;
        }

        .close-icon span {
        font-size: 20px;
        }


        .suggestions-panel {
            background-color: rgb(41, 51, 87); /* Set the panel background to white */
            border: 1px solid 
            rgb(5, 17, 57); /* Optional: Add a border for styling */
            padding: 10px; /* Optional: Add padding for styling */
            list-style: none; /* Remove bullets for list items */
            border-radius: 5px; /* Add border radius for rounded corners */
            max-height: 200px;
            overflow-y: auto; 
        }

        .suggestions-panel li {
                cursor: pointer; /* Change cursor to pointer to indicate clickability */
                color: rgb(250, 246, 246); /* Set font color to black */
        }

        .suggestions-panel li:hover {
                background-color:white; /* Optional: Change background color on hover */
                color:black;
        }
</style>
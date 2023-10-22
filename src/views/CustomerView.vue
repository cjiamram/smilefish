<template>
  <div class="py-4 container-fluid">
    <div class="customer-info" id="customer-info">
                <h6>ข้อมูลลูกค้า</h6>
                <a href="#" class="badge bg-gradient-primary" @click="clearCustomer"><i class="fa fa-refresh" aria-hidden="true">&nbsp;เคลียร์</i></a>&nbsp;
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
      <div class="col-lg-12">
        <form @submit.prevent="postData">
        <div class="row">
          <div class="col-lg-7 mb-lg">
            <!-- Customer Entry -->
            <div class="card z-index-2">

              <div class="card-body">
               
              <div class="row">
                <div class="col-md-12">
                  <label for="example-text-input" class="form-control-label"
                    >ชื่อ-ลูกค้า</label
                  >
                  <input type="hidden" name="id" id="id" v-model="customer.id">
                  <input type="hidden" name="customer_code" id="id" v-model="customer.customer_code">
                  <input name="customer_name" id="customer_name" class="form-control" v-model="customer.customer_name" required  type="text"  />
                </div>
                
                <div class="col-md-6">
                  <label for="example-text-input" class="form-control-label"
                    >โทรศัพท์</label
                  >
                  <input name="telno" id="telno" class="form-control" v-model="customer.telno" type="text"  />
                </div>
                <div class="col-md-6">
                  <label for="example-text-input" class="form-control-label"
                    >Email address</label
                  >
                  <input class="form-control" name="email" id="email" v-model="customer.email" type="email"  />
                </div>
                <div class="col-md-6">
                  <label for="example-text-input" class="form-control-label"
                    >Facebook</label
                  >
                  <input class="form-control" name="facebook" id="facebook" v-model="customer.facebook" type="text"  />
                </div>
                <div class="col-md-6">
                  <label for="example-text-input" class="form-control-label"
                    >Line</label
                  >
                  <input class="form-control" name="line" id="line" type="text" v-model="customer.line"  />
                </div>
                
              </div>

              <div class="row">

                <div class="col-md-12">
                  <label for="example-text-input" class="form-control-label"
                    >ที่อยู่</label
                  >
                  <textarea class="form-control" name="address" id="address" v-model="customer.address"></textarea>
                </div>
                <div class="col-md-12">
                  <label for="example-text-input" class="form-control-label"
                    >ฟาร์ม</label
                  >
                  <input class="form-control" name="farm" id="farm" type="text" v-model="customer.farm"  />
                </div>
                <div class="col-md-6">
                  <label for="example-text-input" class="form-control-label"
                    >จังหวัด</label
                  >
                      <select v-model="customer.province" class="form-control" >
                            <option value="">****เลือกจังหวัด****</option>
                            <option v-for="province in provinces" :key="province.id" :value="province.province_code">
                            {{ province.province_name }}
                            </option>
                      </select>
                </div>
               
                <div class="col-md-6">
                  <label for="example-text-input" class="form-control-label"
                    >รหัสไปรษณีย์</label
                  >
                 <input type="text"  name="postalcode"  id="postalcode" v-model="customer.postalcode" class="form-control">
                </div>
              </div>
              <div class="col-md-6">
                <div>&nbsp;</div>
                <input type="file" class="form-control"    ref="fileInput" @change="handleFileChange">
              </div>

              <hr/>

              <div class="col-md-12">
                  <button class="btn btn-success">บันทึก</button>&nbsp;
                  <a class="btn btn-info" href="#" @click="showModal">ค้นหา</a>
              </div>
            
            </div>
            
            </div>
          </div>
          <div class="col-lg-5">
           <CustomerProfile :picture="this.customerPhoto"/>

          </div>
        </div>
       
        </form>
      </div>
    </div>
    <br/>
   
    <div>
      <InitCustomerQuery :customerList="customers"  :readData="readData"  :deleteData="deleteData" :itemperPage="10"/>
    </div>
    <div>
  
  </div>
  </div>

  <Modal :show="isModalVisible"  :provinces="this.provinces" :closeModal="closeModal" :searchCustomer="searchCustomer">
      <!-- Modal content goes here -->
      <h2>ค้นหาข้อมูล</h2>
      <!-- <p>This is a custom modal component.</p> -->
    </Modal>
 
</template>
<script>
import config from '../config/config.json';
import Swal from 'sweetalert2'
import CustomerProfile from './components/CustomerProfile.vue';
import InitCustomerQuery from './components/InitCustomerQuery.vue';
import Modal from './components/ModalCustomerSearch.vue';

export default {
  name: "customer",



  data() {
    return {
      customer: {
              id:0,
              customer_code:'',
              customer_name: '',
              telno: '',
              facebook: '',
              email:'',
              line: '',
              farm:'',
              address:'',
              province:'',
              postalcode:'',
              created_at: null,
              updated_at: null,
              picture:''
        },
        customers:[],
        provinces: [],
        isModalVisible:false,
        isMainVisible:true,
        selectedFile:null,
        customerPhoto:null,
        extension:'',
        showToggle:true
      }
  },

  mounted() {
    // Fetch data from the API endpoint
    this.fetchProvince();
    this.fetchInitCustomers();
  },

  components:{
    CustomerProfile,
    InitCustomerQuery,
    Modal  
  },
  methods:{

     //********************************************************/ 
     
  
  checkImageSize(file) {
        const maxSizeInBytes = 1 * 1024 * 1024; // 1 MB (adjust this as needed)
        if (file.size > maxSizeInBytes) {
          //console.log('Image size exceeds the maximum limit.');
          return false;
        }
        console.log('Image size is within the limit.');
        return true;
  },

  toggleInput(){
      this.showToggle=!this.showToggle;
      this.isMainVisible=!this.isMainVisible;

  },


  handleFileChange(event) {
      const flag= this.checkImageSize(event.target.files[0]);
      this.selectedFile = event.target.files[0];


      if(flag===true){
          this.customerPhoto = URL.createObjectURL(event.target.files[0]);
        }
      else{
          this.$refs.fileInput.value = '';    
          Swal.fire({
                  title: 'Error!',
                  text: 'รูปภาพมีขนาดใหญ่เกิน 1 MB',
                  icon: 'error',
              });

      }
    },

    /*
    get extension from original file
    */
    getFileExtension() {
      try {
        console.log(this.selectedFile);
        if (this.selectedFile.name !== "") {
            const file = this.selectedFile.name;
            const extension = file.split('.').pop();
            return extension;
        }
        return 'jpg';
    } catch (error) {
        console.error('An error occurred while getting the file extension:', error);
        return null;
    }
    },


   /*
    upload data
   */ 
    
   async uploadFile() {
      if(this.selectedFile!==null){
          const apiUrl=config.apiUrl;
          const formData = new FormData();
          formData.append('file', this.selectedFile);
          this.extension=this.getFileExtension();
          const url=apiUrl+'customer/upload_and_rename/'+this.customer.customer_code+"."+this.extension;
          await fetch(url, {
                method: 'POST',
                body: formData, // Attach the FormData object containing the file
              })
                .then((response) => {
                  if (response.ok) {
                    const json_data=response.json();
                    console.log(json_data);
                  } else {
                    throw new Error('File upload failed.');
                  }
                });
          }
    },

 

  async readData(id){
    const apiUrl=config.apiUrl;
    await fetch(apiUrl+'customer/'+id)
      .then(response => response.json())
      .then(data => {
              this.isMainVisible=true;
              this.customer = data; // Populate the provinces data  
              this.getPicture(this.customer.picture);    
              this.showToggle=true ; 
              document.getElementById("customer-info").scrollIntoView({ behavior: 'smooth' })
      }).catch(error => {
        console.error('Error fetching data:', error);
      });
  },

  /*
  get picture from fast api
  */
  getPicture(picture){
    const apiUrl=config.apiUrl;
    const url=apiUrl+"customer/get_picture/"+picture;
    this.customerPhoto=url ;     
  },

  async deleteData(id){
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
             fetch(apiUrl+'customer/delete/'+id)
              .then(response => response.json())
              .catch(error => {
                console.error('Error to delete:', error);
            });
            Swal.fire('Deleted!', 'Your item has been deleted.', 'success');
            this.clearCustomer();
            this.fetchInitCustomers();
       
      } else if (result.isDenied) {
        Swal.fire('Cancelled', 'Your item is safe :)', 'info');
      }
    });

  },

  async fetchProvince(){
    const apiUrl=config.apiUrl;
    await fetch(apiUrl+'province/get_provinces/')
      .then(response => response.json())
      .then(data => {
        this.provinces = data; // Populate the provinces data
      })
      .catch(error => {
        console.error('Error fetching data:', error);
      });

  },

  async fetchInitCustomers(){
    const apiUrl=config.apiUrl;
    await fetch(apiUrl+'customer/query_limit/100')
      .then(response => response.json())
      .then(data => {
       
        this.customers = data; // Populate the provinces data   
        //console.log(data);    
      })
      .catch(error => {
        console.error('Error fetching data:', error);
      });
  },


  async searchCustomer(keyword,province){
    const apiUrl=config.apiUrl;
    keyword=keyword==""?"%":keyword;
    province=province==""?"%":province;
    const url=apiUrl+'customer/search/'+keyword+'/'+province;
    this.closeModal();   

    await fetch(url)
      .then(response => response.json())
      .then(data => {
        //console.log(data);
        this.customers = data; // Populate the provinces data    
        
      })
      .catch(error => {
        console.error('Error fetching data:', error);
      });
  },

  clearCustomer() {
      // Clear the 'customer' object by assigning a new object with empty values
      this.customer = {
        id: 0,
        customer_code:'',
        customer_name: '',
        telno: '',
        facebook: '',
        email: '',
        line: '',
        farm: '',
        address: '',
        province: '',
        postalcode: '',
        created_at: null,
        updated_at: null,
        picture:''
     }
     this.$refs.fileInput.value = ''; 
     this.isMainVisible=true;
     this.showToggle=true 

 },
  
  showSuccess() {
    Swal.fire({
      title: 'Success!',
      text: 'การบันทึกข้อมูลเสร็จสมบูรณ์แล้ว',
      icon: 'success',
    });
  },

  showModal() {
          this.isModalVisible = true;
          this.isMainVisible=false;
          this.showToggle=false;
        
        },
  closeModal() {
          this.isModalVisible = false;
    },
  
  async setPicture(){
    if(this.selectedFile!==null){
            const apiUrl=config.apiUrl;
            const url=apiUrl+'customer/update_picture/'+this.customer.customer_code+"."+this.extension+"/"+this.customer.customer_code;
            await fetch(url)
              .then(response => response.json())
              .then(data => {
                  console.log(data);
              })
              .catch(error => {
                console.error('Error fetching data:', error);
              });
    }

  },

  async postData() {
        try {
              const apiUrl=config.apiUrl; 
              const url = this.customer.id === 0? `${apiUrl}customer/add/`: `${apiUrl}customer/update/`;
              
              const method= this.customer.id === 0?'POST':'PUT';
              //const method='POST';
              const headers = {
                  'Content-Type': 'application/json',
              };


              this.customer.picture='';
              const requestOptions = {
                  method,
                  headers,
                  body: JSON.stringify(this.customer),
              };

              //console.log(JSON.stringify(this.customer));
              const response = await fetch(url, requestOptions);
              // Handle the response as needed
              if (!response.ok) {
                      throw new Error('Network response was not ok');
              }
              
              let data = await response.json();
              if(method==="POST"){
                  this.customer.customer_code=data.customer_code;
              }

             
              this.uploadFile();
              this.setPicture();
              //console.log(response.json);
              

              if(data.Flag==true){
                    this.clearCustomer();
                    this.showSuccess();
                    if(method==="POST")
                      this.fetchInitCustomers();
                    
              }  
            } catch (error) {
                      console.error('Error posting data:', error);
            }

     },
  }
}
</script>
<style scoped>
        .customer-info {
          display: inline-flex;
          align-items: center; /* จัดให้เนื้อหาตรงกลางตามแนวแกนแนวตั้ง */
        }

        .customer-info h6 {
          margin-right: 10px; /* กำหนดระยะห่างด้านขวาของ <h6> จากปุ่ม */
        }
</style>




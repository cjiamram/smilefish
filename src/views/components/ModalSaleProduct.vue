<template>
      <div class="modal" v-if="show">
        <div class="modal-content">
            <slot>

            </slot>
            <div class="row">
                <div class="col-md-12">
                  <label for="example-text-input" class="form-control-label"
                    >Product</label>
                  <input type="hidden" class="form-control"  >
                  <label class="form-control"  style="color: black; background-color: green;">{{ this.sale.product_name }}</label>
                </div>
                <div class="col-md-12">
                  <label for="customer" class="form-control-label"
                    >ลูกค้า</label>
                  <input name="customer" id="customer" v-model="this.sale.customer" class="form-control" type="text"  />
                </div>
                <div class="col-md-6">
                  <label for="quantity" class="form-control-label"
                    >จำนวน</label>
                  <input name="quantity" id="quantity" v-model="this.sale.sale" class="form-control" type="number"  />
                </div>
                <div class="col-md-6">
                  <label for="created_at" class="form-control-label"
                    >วันที่ขาย</label>
                  <input name="created_at" id="created_at" v-model="this.sale.created_at" class="form-control" type="date"  />
                </div>
            </div>
            <hr/>
            <div class="row">
                <div class="col-md-12">
                <a class="btn btn-danger" @click="close2Parent">ปิด</a>&nbsp;
                <a  class="btn btn-success" @click="sale2Parent">ขาย</a>&nbsp;
            </div>
        </div>
    </div>
  
</template>

<script>
export default {
    data(){
        return {
            sale:{
                product_code:'',
                product_name:'',
                customer_code:'',
                price:0,
                quantity:0

            },
            
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
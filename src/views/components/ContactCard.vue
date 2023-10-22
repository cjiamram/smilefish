<template>
  <div class="card-body p-1">
          <div class="card-body" >  
          <!-- <h6 class="mb-2 text-sm">ติดต่อ</h6> -->
            <span class="mb-3 text-sm">
              โทรศัพท์:
              <span  >{{ TelNo  }}</span>
            </span>
            <span class="mb-1 text-xs" >
              Email:
              <span  >{{ Email }}</span>
            </span>
            <span class="text-xs" >
             Facebook:
              <span  class="mb-1 text-xs">{{FaceBook}}</span>
            </span>
            <span class="text-xs" >
             Line:
              <span   class="mb-1 text-xs">{{Line}}</span>
            </span>
            <span  >
             ฟาร์ม:
              <span   class="mb-1 text-xs">{{Farm}}</span>
            </span>
          </div>
          </div>

</template>

<script>

    //import config from '../config/config.json';



   export default {

    props:{
        TelNo:{
            type:String 
        },
        Email:{
            type:String 
        },
        FaceBook:{
            type:String 
        },
        Line:{
            type:String 
        },
        Farm:{
            type:String 
        },



    }

}
</script>

<style>

</style>
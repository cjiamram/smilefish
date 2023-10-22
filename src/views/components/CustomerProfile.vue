<template>
  <div class="card z-index-2">
    <img src="../../assets/img/bg-sea-2.jpg" alt="Image placeholder" class="card-img-top" />
    <div class="row justify-content-center">
      <div class="col-4 col-lg-4 order-lg-2">
        <div class="mt-n4 mt-lg-n6 mb-4 mb-lg-0">
          <a href="javascript:;">
           <img v-if="picture===null"
              src="../../assets/img/team-2.jpg"  class="rounded-circle img-fluid border border-2 border-white"  
            />
            <img v-else
            :src="this.picture"   class="rounded-circle img-fluid border border-2 border-white" media_type="image/jpeg">
          </a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {

  props:{
    picture:null
  }

}
</script>

<style>

</style>
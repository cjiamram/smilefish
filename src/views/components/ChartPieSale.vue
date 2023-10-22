<template>
    <div class="card">
    <div class="pb-0 card-header mb-0" style="background-color: cadetblue;">
      <h6>{{ title }}</h6>
      <p class="text-sm">
        <i class="fa fa-arrow-up text-success"></i>
        <!-- <span class="font-weight-bold">{{detail}}</span> -->
      
      </p>
    </div>
    <div class="p-3 card-body">
      <div class="chart">
        <canvas id="chart-pie" class="chart-canvas" height="230px"></canvas>
      </div>
    </div>
  </div>
</template>

<script>
import Chart from "chart.js/auto";


export default {
    name: "gradient-bar-chart",

    data(){
       return{
                chart: null,  // Store the Chart instance,
       }

    },

    props: {
            title: {
                type: String,
                default: "สัดส่วนการสั่งซื้อสินค้า",
            },
            detail: {
                type: String,
                default: "",
            },

            datasets:[]
    
    },

    mounted() {

    },


    methods: {

        convert2Array(objects){
            const resultArray = Object.entries(objects).map(([key, value]) => {
                    return { key: parseInt(key), value };
            });
            return resultArray;
        },


    
        createChart(dset) {
                if(dset!=undefined){
                        const labels=dset.labels;
                        const colors=dset.colors;
                        const data=dset.data;
                        this.ctx = document.getElementById('chart-pie').getContext('2d');

                        if (this.pieChart) {
                            this.pieChart.destroy();
                        }

                        this.pieChart = new Chart(this.ctx, {
                            type: 'pie',
                            data: {
                            labels: labels,
                            datasets: [{
                                data: data,
                                backgroundColor: colors
                            }]
                            },
                            options: {
                                    responsive: true,
                                    maintainAspectRatio: false,
                                    display: true,
                                    padding: 10,
                                    color: "#fbfbfb",
                                    font: {
                                        size: 11,
                                        family: "Open Sans",
                                        style: "normal",
                                        lineHeight: 2,
                                    },
                            }
                        }
                        
                );
            }
        },
       
      

  }
}
  
</script>

<style>

</style>
<template>
    <div class="card">
    <div class="pb-0 card-header mb-0"  style="background-color:green">
      <h6>{{ title }}</h6>
      <p class="text-sm">
        <i class="fa fa-arrow-up text-success"></i>
        <!-- <span class="font-weight-bold">{{detail}}</span> -->
      
      </p>
    </div>
    <div class="p-3 card-body" >
      <div class="chart" >
        <canvas id="chart-line"   class="chart-canvas" height="230px"></canvas>
      </div>
    </div>
  </div>
</template>

<script>
import Chart from "chart.js/auto";

export default {
    name: "gradient-line-chart",

    data(){
       return{
                chart: null  // Store the Chart instance
       }

    },

    props: {
            title: {
                type: String,
                default: "รายการการสั่งซื้อ",
            },
            detail: {
                type: String,
                default: "",
            },
            datasets:{
                type: Object,
                default: () => ({})
             },
            
            // data_entries:Array,

        
 
    },
    methods:{

        coverttoArray(objects){

                const resultArray = Object.values(objects);
                return resultArray;
        },
      
        renderChart(dsets){
                // Destroy existing chart if it exists
                if (this.chart) {
                    this.chart.destroy();
                }

                try {

                    const canvas = document.getElementById('chart-line');
                    if (!canvas) {
                        throw new Error('Canvas element not found.');
                    }

                    const context = canvas.getContext('2d');
                    if (!context) {
                        throw new Error('2D context not supported.');
                    }
                    
                    
                    let ctx1 = document.getElementById("chart-line").getContext("2d");
                    let gradientStroke1 = ctx1.createLinearGradient(0, 230, 0, 50);
                    gradientStroke1.addColorStop(1, "rgba(94, 114, 228, 0.2)");
                    gradientStroke1.addColorStop(0.2, "rgba(94, 114, 228, 0.0)");
                    gradientStroke1.addColorStop(0, "rgba(94, 114, 228, 0)");


                    const labels= this.coverttoArray(dsets.labels);
                    //console.log(labels);
                    const sales=this.coverttoArray(dsets.sales);

                    this.chart =new Chart(ctx1, {
                    type: "line",
                    data: {
                        labels:labels,
                        datasets: [
                        {
                            label: "การสั่งซื้อสินค้า",
                            tension: 0.4,
                            borderWidth: 0,
                            pointRadius: 0,
                            borderColor: "#4BB543 ",
                            backgroundColor: gradientStroke1,
                            // eslint-disable-next-line no-dupe-keys
                            borderWidth: 3,
                            fill: true,
                            data: sales,
                            maxBarThickness: 6,
                        },

                      
                        ],
                    },
                    options: {
                        responsive: true,
                        maintainAspectRatio: false,
                        plugins: {
                        legend: {
                            display: false,
                        },
                        },
                        interaction: {
                        intersect: false,
                        mode: "index",
                        },
                        scales: {
                        y: {
                            
                            grid: {
                            drawBorder: false,
                            display: true,
                            drawOnChartArea: true,
                            drawTicks: false,
                            borderDash: [5, 5],
                            },
                            ticks: {
                                callback: function (value) {
                                return value / 1000 + 'k';
                                },
                            display: true,
                            padding: 10,
                            color: "#fbfbfb",
                            font: {
                                size: 11,
                                family: "Open Sans",
                                style: "normal",
                                lineHeight: 2,
                            },
                            },
                        },
                        x: {
                            grid: {
                            drawBorder: false,
                            display: false,
                            drawOnChartArea: false,
                            drawTicks: false,
                            borderDash: [5, 5],
                            },
                            ticks: {
                            display: true,
                            color: "#ccc",
                            padding: 20,
                            font: {
                                size: 11,
                                family: "Open Sans",
                                style: "normal",
                                lineHeight: 2,
                            },
                            },
                        },
                        },
                    },
                });
 
                } catch (error) {
                        console.error('An error occurred while creating the chart:', error);
                }
        },
    },
    }
  
</script>

<style>

</style>
<template>
  <vue-highcharts
    type="chart"
    :options="chartOptions"
    :redrawOnUpdate="true"
    :animateOnUpdate="true"
  />
</template>
<script>
import { computed} from "vue";
import VueHighcharts from "vue3-highcharts";
import StockCharts from "highcharts/modules/drilldown";
import StockCharts2 from "highcharts/modules/exporting";
import HighCharts from "highcharts";
StockCharts(HighCharts);
StockCharts2(HighCharts);

export default {
  name: "SimpleChart",

  components: {
    VueHighcharts,
  },
  data(){
    return{
    }
  },
methods:{
  async fetchData(){
     await fetch(`https://vuejs-78599-default-rtdb.firebaseio.com/"samplePatients".json`)
      .then(data=>{
      return data.json();
    })
    .then(post=>{
      localStorage.setItem('apiData', JSON.stringify(post))
    })
    .catch(error=>{
      console.error(error)
    })
},
},
mounted(){
  //console.log("working")
  this.fetchData();
},

setup() { 
 let apiData= JSON.parse(localStorage.getItem('apiData'));

 let ages= apiData.map((data)=>data.age);
 let bloodGroups=apiData.map((data)=>data.bloodGroup)

    let belowTen= ages.filter((age)=>age>0 && age<=10)
    let belowTwenty= ages.filter((age)=>age>10 && age<=20)
    let belowThirty= ages.filter((age)=>age>20 && age<=30)
    let belowForty= ages.filter((age)=>age>30 && age<=40)
    let belowFifty= ages.filter((age)=>age>40 && age<50)
    let O=bloodGroups.filter((bloodGroup)=>bloodGroup == "O")
    let A=bloodGroups.filter((bloodGroup)=>bloodGroup == "A")
    let B=bloodGroups.filter((bloodGroup)=>bloodGroup == "B")
    let AB=bloodGroups.filter((bloodGroup)=>bloodGroup == "AB")
  let chartData=[
      
      {
        name: "0-10",
        y: belowTen.length,
        color: "green",
      },
      {
        name: "11-20",
        y: belowTwenty.length,
        color: "green",
      },
      {
        name: "21-30",
        y: belowThirty.length,
        color: "green",
      },
      {
        name: "31-40",
        y: belowForty.length,
        color: "green",
      },
      {
        name: "41-50",
        y: belowFifty.length,
        color: "green",
      },
      {
        name: "O",
        y: O.length,
        color: "red",
      },
      {
        name: "A",
        y: A.length,
        color: "red",
      },
      {
        name: "B",
        y: B.length,
        color: "red",
      },
      {
        name: "AB",
        y: AB.length,
        color: "red",
      }
      
    ];
  

    const chartOptions = computed(() => ({
      chart: {
        type: "column",
      },
      title: {
        text: "Patient data",
      },
      accessibility: {
        announceNewData: {
          enabled: true,
        },
      },
      xAxis: [
        {
          id: 0,
          type: "category",
          title: {
          text: "Distribution of Age and BloodGroups",
        }
        }
      ],
      yAxis: {
        title: {
          text: "No of People",
        },
      },
      legend: {
        enabled: false,
      },
      plotOptions: {
        series: {
          borderWidth: 0,
          dataLabels: {
            enabled: true,
            //format: '{point.y:.1f}%'
          },
        },
      },
      tooltip: {
        headerFormat: '<span style="font-size:11px">{series.name}</span><br>',
        formatter: function () {
          var pcnt =
            (this.y /
              this.series.data.map((p) => p.y).reduce((a, b) => a + b, 0)) *
            100;
          return pcnt.toFixed(2) + "%";
        },
      },
      credits: {
        enabled: false,
      },
      series: [
        {
          data: chartData,
        },
      ],
    }));

    return {
      chartOptions,
    };
  },
  watch:{
    fetchData(){
      this.chartOptions
    }
  }


};
</script>
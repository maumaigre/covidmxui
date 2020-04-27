<template>
  <div class="stats">
    <div class="filter-state">
      <v-col>
        <v-select
          :items="entidades"
          label="Filtrar por estado"
          item-text="ENTIDAD_FEDERATIVA"
          item-value="CLAVE_ENTIDAD"
          v-model="selectedState"
        ></v-select>
      </v-col>
    </div>
    <div class="table-container">
      <table>
        <thead>
          <tr>
            <th>Confirmados</th>
            <th>Fallecidos</th>
            <th>Total de pruebas</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>
              {{ countConfirmed }}
            </td>
            <td>
              {{ countDead }}
            </td>
            <td>
              {{ countTested }}
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <v-select
      :items="selectedFilterOptions"
      label="Filtrar por resultado"
      v-model="selectedFilter"
    ></v-select>

    <div style="width: 80%;" v-if="selectedChart == 'stateBar' && stateStats.length">
      <BarChart :chartData="barChartData"/>
      <div class="next-chart">
        <v-btn @click="selectedChart = 'dailyLine'" outlined color="indigo">Sig. gráf.</v-btn>
      </div>
    </div>

    <div style="width: 80%;" v-if="selectedChart == 'dailyLine' && dailyNewStats.length">
      <BarChart :chartData="lineChartData"/>
      <div class="next-chart">
        <v-btn @click="selectedChart = 'stateBar'" outlined color="indigo">Sig. gráf.</v-btn>
      </div>
    </div>
    
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch} from 'vue-property-decorator';

import axios from 'axios';

import entidades from './../etc/entidades'

import BarChart from './BarChart.vue'

@Component({
  components: {BarChart}
})
export default class Stats extends Vue {
  private countTested = 0;
  private countConfirmed = 0;
  private countDead = 0;
  public entidades = entidades;
  public selectedState = "";
  public stateStats: any = [];
  public barChartData: any = {};
  public lineChartData: any = {};
  public dailyNewStats: any = {};

  public selectedChart = "stateBar";
  public selectedChartOptions = ["stateBar", "dailyLine"]


  public selectedFilter = "Confirmados";
  public selectedFilterOptions = ["Confirmados", "Fallecidos"]
  

  mounted() {
    this.getStats();
    this.getStateStats();
    this.getDailyNewStats();
  }

  getStats() {
    axios.get("https://covidmx.live/api/stats").then(res=>{
      const { Tested, Confirmed, Dead } = res.data.count;
      this.countTested = Tested;
      this.countConfirmed = Confirmed;
      this.countDead = Dead;
    });
  }

  getStateStats() {
    axios.get(`https://covidmx.live/api/stateStats?order=desc`).then(res=>{
      this.stateStats = res.data;


      const labels = this.stateStats.map((stateStat: any) =>{
        const entidad = entidades.find(entidad=>{
          return entidad.CLAVE_ENTIDAD == stateStat.EntidadRes;
        });
        return entidad ? entidad["ABREVIATURA"] : "";
      });

      this.barChartData = {
        labels,
        datasets: [
            {
                label: this.selectedFilter === "Confirmados" ? "Casos confirmados" : "Fallecidos",
                backgroundColor: "#52b1b8",
                data: [...this.stateStats.map((stat: any) => this.selectedFilter === "Confirmados" ? stat.Confirmados : stat.Fallecidos)]
            }
        ]
      }
    });
  }

  getDailyNewStats() {
    axios.get("https://covidmx.live/api/dailyNewStats").then(res=>{
      this.dailyNewStats = res.data;
      this.lineChartData = {
        labels: this.dailyNewStats.map((stat: any) => {
          const date = stat.FechaIngreso.split("-")
          return `${date[1]}/${date[2]}`
        }),
        datasets: [
            {
                label: this.selectedFilter === "Confirmados" ? "Nuevos casos confirmados por día" : "Nuevos fallecidos por día",
                backgroundColor: "#52a1f8",
                data: [...this.dailyNewStats.map((stat: any) => this.selectedFilter === "Confirmados" ? stat.NewConfirmed : stat.NewDead)]
            }
        ]
      }
    });
  }

  @Watch('selectedState')
  onSelectedStateChanged(value: string) {
    if (!value) this.getStats();
    else axios.get(`https://covidmx.live/api/stats?entidad_res=${value}`).then(res=>{
      const { Tested, Confirmed, Dead } = res.data.count;
      this.countTested = Tested;
      this.countConfirmed = Confirmed;
      this.countDead = Dead;
    });
  }

  @Watch('selectedFilter')
  onSelectedFilterChanged(value: string) {
    this.getStateStats();
    this.getDailyNewStats();
  }
  
  
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.stats{
  display: flex;
  flex-flow: column wrap;
  align-items: center;
}
.table-container {
  display: flex;
  justify-content: center;
  margin-bottom: 2%;
  table{
    tr th{
      width: 150px;
    }
  }
}

.filter-state{
  width: 80%;
  padding: 2% 4%;
}

.next-chart{
  margin-top: 2%;
}

</style>

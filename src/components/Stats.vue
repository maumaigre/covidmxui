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
              {{ countTotal }}
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <div style="width: 80%;" v-if="stateStats.length">
      <BarChart :chartData="barChartData"/>
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
  private countTotal = 0;
  private countConfirmed = 0;
  private countDead = 0;
  public entidades = entidades;
  public selectedState = "";
  public stateStats: any = [];
  public barChartData: any = {};

  mounted() {
    this.getStats();
    this.getStateStats();
  }

  getStats() {
    axios.get("https://covidmx.live/api/stats").then(res=>{
      const { Total, Confirmed, Dead } = res.data.count;
      this.countTotal = Total;
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
                label: "Casos confirmados",
                backgroundColor: "#52b1b8",
                data: [...this.stateStats.map((stat: any) => stat.Casos)]
            }
        ]
      }
    });
  }

  @Watch('selectedState')
  onPropertyChanged(value: string) {
    if (!value) this.getStats();
    else axios.get(`https://covidmx.live/api/stats?entidad_res=${value}`).then(res=>{
      const { Total, Confirmed, Dead } = res.data.count;
      this.countTotal = Total;
      this.countConfirmed = Confirmed;
      this.countDead = Dead;
    });
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

</style>

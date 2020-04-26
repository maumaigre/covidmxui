<template>
  <div class="table">
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
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import axios from 'axios';

@Component
export default class Stats extends Vue {
  private countTotal = 0;
  private countConfirmed = 0;
  private countDead = 0;

  mounted() {
    axios.get("https://covidmx.live/api/stats").then(res=>{
      const { Total, Confirmed, Dead } = res.data.count;
      this.countTotal = Total;
      this.countConfirmed = Confirmed;
      this.countDead = Dead;
    })
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
table{
  tr th{
    width: 150px;
  }
}

</style>

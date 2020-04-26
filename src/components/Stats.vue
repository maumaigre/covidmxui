<template>
  <div class="stats">
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

    <div>
      <a-select defaultValue="lucy" style="width: 120px" @change="handleChange">
      <a-select-option value="jack">Jack</a-select-option>
      <a-select-option value="lucy">Lucy</a-select-option>
      <a-select-option value="disabled" disabled>Disabled</a-select-option>
      <a-select-option value="Yiminghe">yiminghe</a-select-option>
      </a-select>
      <select>
        <option
          v-for="entidad of entidades"
          v-bind:key="entidad.CLAVE_ENTIDAD">{{entidad.ENTIDAD_FEDERATIVA}}</option>
      </select>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import axios from 'axios';

import entidades from './../etc/entidades'

@Component
export default class Stats extends Vue {
  private countTotal = 0;
  private countConfirmed = 0;
  private countDead = 0;
  private entidades = entidades;

  mounted() {
    axios.get("https://covidmx.live/api/stats").then(res=>{
      const { Total, Confirmed, Dead } = res.data.count;
      this.countTotal = Total;
      this.countConfirmed = Confirmed;
      this.countDead = Dead;
    });
    console.log(entidades);
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

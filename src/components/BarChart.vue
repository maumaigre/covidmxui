<script lang="ts">

import { Component, Vue, Watch} from 'vue-property-decorator';
import { Bar, mixins } from 'vue-chartjs'
const { reactiveProp } = mixins;

@Component({
  extends: Bar,
  mixins: [reactiveProp],
})

export default class BarChart extends Vue {
    public options = {
        responsive: true,
        maintainAspectRatio: false
    }

    public renderChart!: (chartData: any, options: any) => void

    mounted(){
        console.log(this.$props.chartData)
        this.renderChart(this.$props.chartData, this.options)
    }
  
}

</script>
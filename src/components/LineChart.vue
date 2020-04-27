<script lang="ts">

import { Component, Vue, Watch} from 'vue-property-decorator';
import { Line, mixins } from 'vue-chartjs'
const { reactiveProp } = mixins;

@Component({
  extends: Line,
  mixins: [reactiveProp],
})

export default class LineChart extends Vue {
    public options = {
        responsive: true,
        maintainAspectRatio: false
    }

    public renderChart!: (chartData: any, options: any) => void

    mounted(){
        this.renderChart(this.$props.chartData, this.options)
    }
  
}

</script>
<template>
  <div class="home">
    <Stats/>
    <div class="footer">
      <a href="https://coronavirus.gob.mx/">
        <img style="width: 140px;" src="https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/SALUD_Logo_2019.svg/600px-SALUD_Logo_2019.svg.png"/>
      </a>

      <a href="https://github.com/maumaigre/covidmx">
        <img style="width: 30px;" src="https://upload.wikimedia.org/wikipedia/commons/9/91/Octicons-mark-github.svg"/>
      </a>
    </div>

  
  </div>
</template>

<script>
// @ is an alias to /src
import Stats from '@/components/Stats.vue'

export default {
  name: 'Home',
  components: {
    Stats
  }
}
</script>
<style lang="scss" scoped>
.footer{
  margin-top: 5%;
  justify-content: center;
  display: flex;
  align-items: center;
  a:not(:first-child){
    margin-left: 5%;
  }
}
</style>

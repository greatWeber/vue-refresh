<template>
  <div>
    <my-refresh ref="refresh" @refresh="refreshCb" :maxRange="200"></my-refresh>
    <div class="hello">
      <h1>{{ msg }}</h1>
      <h2>vue--下拉刷新!!!</h2>
    </div>
    
  </div>
</template>

<script>
import myRefresh from './refresh.vue';
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App'
    }
  },
  components: {
    myRefresh
  },

  methods: {
    refreshCb(){
      let _this = this;
      setTimeout(()=>{
        _this.$refs.refresh.refreshEnd();
        
      },2000)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1 {
  font-size: 0.5rem;
}
h2 {
  font-size: 0.4rem;
}
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
  font-size: 0.3rem;
}
a {
  color: #42b983;
}
</style>

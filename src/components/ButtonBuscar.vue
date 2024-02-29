<template>
  <button class="botao">Buscar</button>
</template>

<script>
  export default {
    name: 'ButtonBuscar'
  }
</script>


<style scoped>
button{
  font-family: SansSerif;
  background-color: #3cb13c;
  border: 0px;
  border-radius: 5px;
  font-size: 20px;
  padding: 5px 15px;
  color: white;
  font-weight: bold;
}

button:hover{
  background-color: #3ea43e;
  transition: 0.1s;
  cursor: pointer;
}

button:active{
  background-color: #40b440;
}

</style>
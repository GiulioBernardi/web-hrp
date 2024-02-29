<template>
  <div class="saidas">
    <BaseInput tipoInput="number"
               v-model="valorSaidaFiltro"
               inputLabel="Valor"/>

    <BaseInput tipoInput="text"
               v-model="descricaoSaidaFiltro"
               inputLabel="Descricao"/>

    <BaseInput tipo-input="date"
               v-model="dataSaidaFiltro"
               input-label="Data"/>

    <ButtonBuscar @click="buscar"/>
    <table v-show="saidas.length > 0" class="grid-saidas">
      <thead >
      <th class="grid-head-saidas" v-for="coluna in colunas">{{coluna.campo}}</th>
      </thead>
      <tr v-for="saida in saidas">
        <td class="grid-linha-saidas">R$ {{saida.valor}}</td>
        <td class="grid-linha-saidas">{{saida.descricao}}</td>
        <td class="grid-linha-saidas">{{saida.data}}</td>
      </tr>
    </table>
  </div>
</template>

<script>

import BaseInput from '../components/BaseInput.vue'
import ButtonBuscar from "@/components/ButtonBuscar.vue";
import axios from "axios";
export default {
  name: 'saidasView',
  components:{
    BaseInput,
    ButtonBuscar
  },
  data(){
    return {
      baseUrl: 'http://localhost:8080/gasto',
      valorSaidaFiltro: null,
      descricaoSaidaFiltro: null,
      dataSaidaFiltro: null,
      saidas: []
    }
  },
  methods:{
    formatarData(data){
      //esse metodo é util
      //todo colocar esse método em uma classe geral para visualização de todos os arqvs
      var dia = data.substring(8,10)
      var mes = data.substring(5,7)
      var ano = data.substring(0,4)
      return dia+'/'+mes+'/'+ano;
    },
    buscar(){
      var saidaFilter;
      saidaFilter = {
        dataSaida: this.dataSaidaFiltro
      }
      axios.post(this.baseUrl + '/obter', saidaFilter).then(resposta => {
        resposta.data.forEach(saida => saida.data = this.formatarData(saida.data))
        this.saidas = resposta.data
      }).catch(erro => {
        console.log(erro.message);
      })
    }
  }
}
</script>



<style>
.saidas{
  margin-left: 26%;
}
.formulario-saidas{

}

.grid-saidas, .grid-head-saidas, .grid-linha-saidas {
  border: 1px solid;
  border-collapse: collapse;
}

.grid-saidas{
  width: 60%;
}
</style>
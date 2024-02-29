<template>
  <div class="entradas">
    <form class="formulario-entradas">
      <BaseInput tipoInput="number"
                 v-model="valorEntradaFiltro"
                 inputLabel="Valor"/>

      <BaseInput tipoInput="text"
                 v-model="descricaoEntradaFiltro"
                 inputLabel="Descricao"/>

      <BaseInput tipo-input="date"
                 v-model="dataEntradaFiltro"
                 input-label="Data"/>

    </form>
    <button @click="toggleModal">Adicionar Entrada</button>
    <ModalAdicionarEntrada :inclusao="'entrada'"
                           @fecharModal="toggleModal"
                           @salvarModal="salvarEntrada"
                           :modalAberto="modalAberto"/>

    <ButtonBuscar @click="buscar(false)" />
    <h3 v-show="entradas.length == 0">Não há informações</h3>
    <table v-show="entradas.length > 0" class="grid-entradas">
      <thead >
        <th class="grid-head-entradas" v-for="coluna in colunas">{{coluna.campo}}</th>
      </thead>
      <tr v-for="entrada in entradas">
        <td class="grid-linha-entradas">R$ {{entrada.valor}}</td>
        <td class="grid-linha-entradas">{{entrada.descricao}}</td>
        <td class="grid-linha-entradas">{{entrada.data}}</td>
      </tr>
    </table>
  </div>
</template>

<script>
import axios from 'axios';
import ButtonBuscar from "@/components/ButtonBuscar.vue";
import BaseInput from "@/components/BaseInput.vue";
import ModalAdicionarEntrada from "@/components/ModalAdicionarEntrada.vue";
  export default {
    name: 'entradasView',
    components: {BaseInput, ButtonBuscar, ModalAdicionarEntrada},
    data(){
      return {
        baseUrl: 'http://localhost:8080/entrada',
        valorEntradaFiltro: 0,
        descricaoEntradaFiltro: '',
        dataEntradaFiltro: '',
        entradas: [],
        colunas: [
          { campo: "Valor" },
          { campo: "Descrição" },
          { campo: "Data" }
        ],
        modalAberto: false,
      }
    },
    methods:{
      formatarData(data){
        var dia = data.substring(8,10)
        var mes = data.substring(5,7)
        var ano = data.substring(0,4)
        return dia+'/'+mes+'/'+ano;
      },
      salvarEntrada(adicionarEntradaForm){
        axios.post(this.baseUrl + '/adicionar-entrada', adicionarEntradaForm)
            .then(function (response) {
        })
        console.log('to aq')
        debugger;
        this.buscar(true);
        this.modalAberto = false;
      },
      buscar(salvou){
        var entradaFilter;
        if(salvou){
          entradaFilter = {
            dataEntrada: null
          }
        }else {
          entradaFilter = {
            dataEntrada: this.dataEntradaFiltro
          }
        }

        axios.post(this.baseUrl + '/obter', entradaFilter).then(resposta => {
            resposta.data.forEach(entrada => entrada.data = this.formatarData(entrada.data))
            this.entradas = resposta.data
        }).catch(erro => {
          console.log(erro.message);
        })
      },
      toggleModal(){
        this.modalAberto = !this.modalAberto;
      }
    }

  }
</script>


<style>
.entradas{
  margin-left: 26%;
}
.formulario-entradas{

}

.grid-entradas, .grid-head-entradas, .grid-linha-entradas {
  border: 1px solid;
  border-collapse: collapse;
}

.grid-entradas{
  width: 60%;
}

</style>
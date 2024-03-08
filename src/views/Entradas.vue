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
                           :modalAberto="modalAberto"
                           :id-editar="this.idEditar"
                           :valor-editar="this.valorEditar"
                           :descricao-editar="this.descricaoEditar"
                           :data-editar="this.dataEditar"/>

    <ButtonBuscar @click="buscar(false)" />
    <h3 v-show="entradas.length == 0">Não há informações</h3>
    <table v-show="entradas.length > 0" class="grid-entradas">
      <thead >
        <th class="grid-head-entradas" v-for="coluna in colunas">{{coluna.campo}}</th>
        <th class="grid-head-entradas">Ações</th>
        <th class="grid-head-entradas">Ações</th>
      </thead>
      <tr v-for="entrada in entradas">
        <td hidden>{{entrada.entradaId}}</td>
        <td class="grid-linha-entradas">R$ {{entrada.valor}}</td>
        <td class="grid-linha-entradas">{{entrada.descricao}}</td>
        <td class="grid-linha-entradas">{{entrada.data}}</td>
        <td class="action excluir"><font-awesome-icon @click="editar(entrada.entradaId, entrada.valor, entrada.descricao, entrada.data)" icon="edit"/></td>
        <td class="action excluir"><font-awesome-icon @click="excluir(entrada.entradaId)" icon="trash"/></td>
      </tr>
    </table>
  </div>
</template>

<script>
import axios from 'axios';
import ButtonBuscar from "@/components/ButtonBuscar.vue";
import BaseInput from "@/components/BaseInput.vue";
import ModalAdicionarEntrada from "@/components/ModalAdicionarMovimentacao.vue";
import {FontAwesomeIcon} from "@fortawesome/vue-fontawesome";
  export default {
    name: 'entradasView',
    components: {BaseInput, ButtonBuscar, ModalAdicionarEntrada, FontAwesomeIcon},
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
        idEditar: '',
        valorEditar: '',
        descricaoEditar: '',
        dataEditar: new Date()
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
        if(adicionarEntradaForm.editou === false){
          axios.post(this.baseUrl + '/adicionar-entrada', adicionarEntradaForm)
              .then(function (response) {
              })
        }else if(adicionarEntradaForm.editou === true) {
          var adicionarEntradaFormEdicao = {
            entradaId: adicionarEntradaForm.id,
            valor: adicionarEntradaForm.valor,
            descricao: adicionarEntradaForm.descricao,
            data: adicionarEntradaForm.data
          }
          console.log(adicionarEntradaFormEdicao);
          axios.put(this.baseUrl + '/editar', adicionarEntradaFormEdicao)
              .then(resposta => alert('Entrada atualizada com sucesso'));
        }
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
      },
      editar(entradaId, valor, descricao, data){
        this.idEditar = Number(entradaId);
        this.valorEditar = Number(valor);
        this.descricaoEditar = String(descricao);
        var dateParts = data.split("/");
        var dateObject = +dateParts[2] + dateParts[1] - 1 + +dateParts[0];
        this.dataEditar = (dateObject);
        this.toggleModal();
      },
      excluir(entradaId){
        axios.delete(this.baseUrl + '/excluir/' + entradaId)
            .then(resposta => {
                console.log('Entrada removida com sucesso');
                this.buscar(true);
            })
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
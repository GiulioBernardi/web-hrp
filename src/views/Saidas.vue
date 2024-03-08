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

    <button @click="toggleModal">Adicionar Saida</button>
    <ModalAdicionarEntrada :inclusao="'saida'"
                           @fecharModal="toggleModal"
                           @salvarModal="salvarSaida"
                           :modalAberto="modalAberto"
                           :id-editar="this.idEditar"
                           :valor-editar="this.valorEditar"
                           :descricao-editar="this.descricaoEditar"
                           :data-editar="this.dataEditar"/>

    <ButtonBuscar @click="buscar"/>
    <h3 v-show="saidas.length == 0">Não há informações</h3>
    <table v-show="saidas.length > 0" class="grid-saidas">
      <thead >
      <th class="grid-head-saidas" v-for="coluna in colunas">{{coluna.campo}}</th>
      </thead>
      <tr v-for="saida in saidas">
        <td hidden>{{saida.saidaId}}</td>
        <td class="grid-linha-saidas">R$ {{saida.valor}}</td>
        <td class="grid-linha-saidas">{{saida.descricao}}</td>
        <td class="grid-linha-saidas">{{saida.data}}</td>
        <td class="action excluir"><font-awesome-icon @click="editar(saida.saidaId, saida.valor, saida.descricao, saida.data)" icon="edit"/></td>
        <td class="action excluir"><font-awesome-icon @click="excluir(saida.saidaId)" icon="trash"/></td>
      </tr>
    </table>
  </div>
</template>

<script>

import BaseInput from '../components/BaseInput.vue'
import ButtonBuscar from "@/components/ButtonBuscar.vue";
import axios from "axios";
import ModalAdicionarEntrada from "@/components/ModalAdicionarMovimentacao.vue";
import {FontAwesomeIcon} from "@fortawesome/vue-fontawesome";
export default {
  name: 'saidasView',
  components:{
    ModalAdicionarEntrada,
    BaseInput,
    ButtonBuscar,
    FontAwesomeIcon
  },
  data(){
    return {
      baseUrl: 'http://localhost:8080/saida',
      valorSaidaFiltro: null,
      descricaoSaidaFiltro: null,
      dataSaidaFiltro: null,
      saidas: [],
      modalAberto: false,
    }
  },
  methods:{

    editar(saidaId, valor, descricao, data){
      console.log('saaasas ', saidaId )
      this.idEditar = Number(saidaId);
      this.valorEditar = Number(valor);
      this.descricaoEditar = String(descricao);
      var dateParts = data.split("/");
      var dateObject = +dateParts[2] + dateParts[1] - 1 + +dateParts[0];
      this.dataEditar = (dateObject);
      this.toggleModal();
    },
    excluir(saidaId){
      axios.delete(this.baseUrl + '/excluir/' + saidaId)
          .then(resposta => {
            console.log('Entrada removida com sucesso');
            this.buscar(true);
          })
    },
    formatarData(data){
      var dia = data.substring(8,10)
      var mes = data.substring(5,7)
      var ano = data.substring(0,4)
      return dia+'/'+mes+'/'+ano;
    },
    salvarSaida(adicionarSaidaForm){
      if(adicionarSaidaForm.editou === false){
        axios.post(this.baseUrl + '/adicionar-saida', adicionarSaidaForm)
            .then(function (response) {
        })
      }else if(adicionarSaidaForm.editou === true) {
        var adicionarSaidaFormEdicao = {
          saidaId: adicionarSaidaForm.id,
          valor: adicionarSaidaForm.valor,
          descricao: adicionarSaidaForm.descricao,
          data: adicionarSaidaForm.data
        }
        console.log(adicionarSaidaFormEdicao);
        axios.put(this.baseUrl + '/editar', adicionarSaidaFormEdicao)
            .then(resposta => alert('Entrada atualizada com sucesso'));
      }
      this.buscar();
      this.modalAberto = false;
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
    },
    toggleModal(){
      this.modalAberto = !this.modalAberto;
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
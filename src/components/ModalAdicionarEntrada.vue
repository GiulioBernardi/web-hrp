<template>
  <transition name="animacao-modal">
    <div class="modal" v-show="modalAberto">
      <div class="erros" v-show="this.erros.length>0">
        <p v-for="erro in erros">{{erro}}</p>
      </div>
      <transition name="animacao-modal-inner">
        <div class="modal-inner" v-show="modalAberto">
          <slot/>
          <h3 class="header-modal">Incluir {{ inclusao }}</h3>
          <form class="form-inputs">
            <BaseInput tipoInput="number"
                       v-model="valor"
                       inputLabel="Valor"/>

            <BaseInput tipoInput="text"
                       v-model="descricao"
                       inputLabel="Descricao"/>

            <BaseInput tipo-input="date"
                       v-model="data"
                       input-label="Data"/>
          </form>

          <div class="botoes-acoes">
            <button class="botao-acao" id="botao-fechar" @click="$emit('fecharModal')">Fechar</button>
            <button class="botao-acao" id="botao-salvar" @click="salvarModal">Salvar</button>
          </div>
        </div>
      </transition>
    </div>

  </transition>
</template>

<script>
import BaseInput from '@/components/BaseInput.vue'

export default {
  props: {
    modalAberto: Boolean,
    inclusao: {String, default: ''}
  },
  components: {BaseInput},
  emits: ['fecharModal', 'salvarModal'],
  data(){
    return{
      valor: null,
      descricao: null,
      data: null,
      erros: []
    }
  },
  methods:{

    validarDados(){
      console.log(this.valor)
      if(this.valor == null || this.valor == ""){
        this.erros.push("O valor da " + this.inclusao + " deve ser informado");
      }else if(this.valor.length > 9){
        this.erros.push("O valor da " + this.inclusao + " não pode ser maior do que 1.000.000");
      }
      setTimeout(() => {
        this.limpaErros();
      }, 4000)

    },
    salvarModal(){
      this.limpaErros();
      this.validarDados();
      var naoTemErros = this.erros.length == 0
      if(naoTemErros){
        var obj = {
          valor:this.valor
        }
        this.$emit('salvarModal', {
          valor: this.valor,
          descricao: this.descricao,
          data: this.data
        })
      }
      this.limpaCampos();

    },
    limpaCampos(){
      this.valor = null;
      this.descricao = null;
      this.data = null;
    },
    limpaErros(){
      this.erros = []
    }
  }
}

</script>

<style scoped>

  .botoes-acoes{
    right: 0;
    position: absolute;
    bottom: 0;
    margin-right: 20px;
    margin-bottom: 20px;
  }

  .botao-acao{
    margin-left: 15px;
  }

  .botao-acao:hover{
    cursor: pointer;
  }

  #botao-fechar{
    font-family: SansSerif;
    background-color: #a8a8a8;
    border: 0;
    border-radius: 5px;
    font-size: 20px;
    padding: 5px 15px;
    color: white;
    font-weight: bold;
  }

  .erros{
    background-color: rgba(255, 0, 0, 0.31);
    top: 0;
    position: absolute;
    right: 0;
    border-radius: 10px;
  }

  #botao-salvar{
    font-family: SansSerif;
    background-color: #033eff;
    border: 0px;
    border-radius: 5px;
    font-size: 20px;
    padding: 5px 15px;
    color: white;
    font-weight: bold;
  }


  #botao-salvar:hover{
    background-color: #0029ab;
    transition: 0.1s;
  }
  #botao-fechar:hover{
    background-color: #7a7a7a;
    transition: 0.1s;
  }

  .modal{
    background-color: rgba(222, 222, 222, 0.7);
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    width: 100vw;
    position: absolute;
    top: 0;
    left: 0;

    .modal-inner{
      position: absolute;
      max-width: 700%;
      width: 1000px;
      height: 30%;
      box-shadow: 0 4px 6px 3px rgba(0,0,0,0.1), 0 2px 4px -1px rgba(0,0,0,0.06);
      border-radius: 10px;
      background-color: #fff;
      padding: 0 2% 2% 2%;
      margin-left: 250px;

    }
  }
  .animacao-modal-enter-active, .animacao-indo-leave-active{
    transition: opacity .3s cubic-bezier(0.52, 0.02, 0.19, 1.02);
  }
  .animacao-modal-enter-from, .animacao-modal-leave-to{
    transform: scale(0.8);
    opacity: 0;
  }
  .animacao-modal-inner-enter-active{
    transition: all .3s cubic-bezier(0.52, 0.02, 0.19, 1.02) 0.15s;
  }
  .animacao-modal-inner-leave-active{
    transition: all .3s cubic-bezier(0.52, 0.02, 0.19, 1.02);
  }
  .animacao-modal-inner-enter-from{
    opacity: 0;
    transform: scale(0.8);
  }
  .animacao-modal-inner-leave-to{
    transform: scale(0.8);
  }

</style>
<template>
  <nav class="barra-navegacao">
    <div class="secoes">
      <router-link class="link-menu" to="/">Home</router-link>
      <div class="secao-movimentacoes">
        <div>
          Movimentações
        </div>
        <router-link class="link-menu" to="/entradas">Entradas</router-link> |
        <router-link class="link-menu" to="/saidas">Saidas</router-link>
      </div>
    </div>

  </nav>
  <router-view/>
</template>

<style>
nav{
  position: absolute;
  //width: 20%;
  width: 275px;
  background-color: #bebebe;
  height: 100%;
}
.link-menu{
  text-decoration: none;
  color: inherit;
}
.secoes{
  width: 100%;
  left: 50%;
}

.barra-navegacao{

}

</style>

<script>
</script>
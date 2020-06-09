<!-- alurapic/src/App.vue -->

<template>
  <div>
    <h1 class="centralizado">{{ titulo }}</h1>

    <p v-show="mensagem" class="centralizado">{{ mensagem }}</p>
    <input type="text" class="filtro" @input="filtro=$event.target.value"
           placeholder="Filtre por parte do título"></input>

    <ul class="lista-fotos">
      <li class="lista-fotos-item" v-for="foto in fotosComFiltro">

        <meu-painel :titulo="foto.titulo">
          <imagem-responsiva :url="foto.url" :titulo="foto.titulo" v-meu-transform:scale.animate="1.2"/>
          <meu-botao
            tipo="button"
            rotulo="REMOVER"
            @botaoAtivado="remove(foto)"
            :confirmacao="true"
            estilo="perigo"/>
        </meu-painel>

      </li>
    </ul>

  </div>
</template>

<script>
  import Painel from '../shared/painel/Painel';
  import ImagemResponsiva from "../shared/imagem-responsiva/ImagemResponsiva";
  import Botao from "../shared/botao/Botao";
  import FotoService from "../../domain/foto/FotoService";

  export default {
    components: {
      'meu-painel': Painel,
      'imagem-responsiva': ImagemResponsiva,
      'meu-botao': Botao
    },
    data() {
      return {
        titulo: 'Alurapic',
        fotos: [],
        filtro: '',
        mensagem: ''
      }
    },

    computed: {
      fotosComFiltro() {
        if (this.filtro) {
          let exp = new RegExp(this.filtro.trim(), 'i');
          return this.fotos.filter(foto => exp.test(foto.titulo));
          return [];
        } else {
          return this.fotos;
        }
      }
    },

    methods: {
      remove(foto) {
        this.service.apaga({id: foto._id})
          .then(() => {
            let indice = this.fotos.indexOf(foto);
            this.fotos.splice(indice, 1);
            this.mensagem = 'Foto removida com sucesso';
          }, err => {
            console.log(err);
            this.mensagem = 'Não foi possível remover a foto';

          });
      }
    },

    created() {

      this.service = new FotoService(this.$resource);

      this.service.lista()
        .then(fotos => this.fotos = fotos, err => console.log(err));
    }
  }
</script>

<style>

  .centralizado {
    text-align: center;
  }

  .lista-fotos {
    list-style: none;
  }

  .lista-fotos .lista-fotos-item {
    display: inline-block;
  }


  .filtro {
    display: block;
    width: 100%;
  }
</style>

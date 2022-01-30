<template>
  <div>
    <v-layout :wrap="true">
      <v-flex xs12 md6>
        <v-card color="error" dark>
          <v-date-picker v-model="fecha" 
            full-width 
            locale="es" 
            :min ="min"
            :max="max"
            @change="getDolar(fecha)">
          </v-date-picker>
        </v-card>
        <v-card color="error" dark>
          <v-card-text class="display-1 text-center">Valor del Dolar: {{precio}}</v-card-text>
        </v-card>
      </v-flex>
    </v-layout>
    
  </div>
</template>

<script>
import axios from "axios";
import {mapMutations} from 'vuex';
  export default {
    name: 'Home',
    data() {
      return {
        fecha: new Date().toISOString().substr(0,10),
        min: '1984',
        max: new Date().toISOString().substr(0,10),
        precio: null
      }
    },
    methods: {
      ...mapMutations(['setLoading','ocultarLoading']),

      async getDolar(dia){
          let array= dia.split('-');
          let yymmdd = array[2]+'-'+array[1]+'-'+array[0];
          try {

            this.setLoading({titulo: 'Accediendo a la Información', color: 'primary'});

            let datos = await axios(`https://mindicador.cl/api/dolar/${yymmdd}`);
            if (datos.data.serie.length > 0) {
              this.precio = datos.data.serie[0].valor;
            } else {
              this.precio = 'Sin Resultados'
            }  
          }catch(error){
            console.log(error);
          }finally{
            this.ocultarLoading();
          }
      }
    },
    created(){
      this.getDolar(this.fecha);
    }
  }
</script>

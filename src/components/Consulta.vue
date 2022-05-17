<template>
    <div>    
        <div v-show="visibilidadeInputs">
            <v-card class="d-none d-sm-block mt-8 ml-3 mr-3" elevation="0">
                <v-card-title class="justify-center" text="h2">Simulador de Empretimos</v-card-title>
                <v-list-item>
                    <v-col cols="6">
                        <v-text-field-money
                            v-model="valor_emprestimo"
                            label="Valor do empréstimo"
                            class="mt-6"
                            v-bind:properties="{
                                prefix: 'R$',
                                readonly: false,
                                disabled: false,
                                outlined: true,
                                clearable: true,
                                placeholder: ' ',
                            }"
                            v-bind:options="{
                                locale: 'pt-BR',
                                length: 11,
                                precision: 2,
                                empty: null,
                            }"
                        />
                    </v-col>
                    <v-col cols="6">
                        <v-autocomplete
                            label="Instituição"
                            v-model="instituicoesFilter"
                            :items="instituicoes"
                            class="mt-6"
                            outlined
                            chips
                            small-chips
                            multiple
                            clearable
                            color = primary
                        ></v-autocomplete>
                    </v-col>
                </v-list-item>

                <v-list-item>
                    <v-col cols="6">
                        <v-autocomplete
                            label="Convênios"
                            v-model="conveniosFilter"
                            :items="convenios"
                            outlined
                            chips
                            small-chips
                            multiple
                            clearable
                        ></v-autocomplete>
                    </v-col>
                    <v-col cols="6">
                        <v-autocomplete
                            label="Parcelas"
                            v-model="parcela"
                            :items="parcelas"
                            outlined
                            clearable
                        ></v-autocomplete>
                    </v-col>
                </v-list-item>

                <div class="text-center pa-6">
                    <v-btn x-large width="300" color="primary"  v-on:click="simular">SIMULAR</v-btn>
                </div>
            </v-card>

            <v-card class="d-sm-none mt-2" elevation="0">
                <v-card-title class="justify-center" color="#ff8000">Simulador de Empretimos</v-card-title>
                <v-list-item>
                    <v-col cols="12">
                        <v-text-field-money
                            v-model="valor_emprestimo"
                            label="Valor do empréstimo"
                            class="mt-6"
                            v-bind:properties="{
                                prefix: 'R$',
                                readonly: false,
                                disabled: false,
                                outlined: true,
                                clearable: true,
                                placeholder: ' ',
                            }"
                            v-bind:options="{
                                locale: 'pt-BR',
                                length: 11,
                                precision: 2,
                                empty: null,
                            }"
                        />
                    </v-col>
                </v-list-item>

                <v-list-item>
                    <v-col cols="12">
                        <v-autocomplete
                            label="Instituição"
                            :items="instituicoes"
                            outlined
                            chips
                            small-chips
                            multiple
                            clearable
                        ></v-autocomplete>
                    </v-col>
                </v-list-item>

                <v-list-item>
                    <v-col cols="12">
                        <v-autocomplete
                            label="Convênios"
                            :items="convenios"
                            outlined
                            chips
                            small-chips
                            multiple
                            clearable
                        ></v-autocomplete>
                    </v-col>
                </v-list-item>

                <v-list-item>
                    <v-col cols="12">
                        <v-autocomplete
                            label="Parcelas"
                            :items="parcelas"
                            outlined
                            clearable
                        ></v-autocomplete>
                    </v-col>
                </v-list-item>

                
                <div class="text-center">
                    <v-btn x-large width="300" color="primary" v-on:click="simular">SIMULAR</v-btn>
                </div>
            </v-card>
        </div>

        <div v-show="visibilidadeParcelas">
            <v-card height="100vh">
                <v-simple-table>
                <template v-slot:default>
                    <thead>
                    <tr>
                        <th class="text-left indigo darken-3 white--text">
                        Instituição
                        </th>
                        <th class="text-left indigo darken-3 white--text">
                        Valor solicitado
                        </th>
                        <th class="text-left indigo darken-3 white--text">
                        Juros ao Mês
                        </th>
                        <th class="text-left indigo darken-3 white--text">
                        Parcelas x Valor
                        </th>
                        
                    </tr>
                    </thead>
                    <tbody>
                    <tr
                        v-for="parcela in parcelasCompleta" :key="parcela.id"
                    >
                        <td>{{ parcela.instituicao }}</td>
                        <td>{{ valor_emprestimo }} R$</td>
                        <td>{{ parcela.taxa }}</td>
                        <td class="orange--text">{{ parcela.parcelas}} x {{ parcela.valor_parcela }} R$</td>
                    </tr>
                    
                    </tbody>
                    
                </template>
                </v-simple-table>
                <div class="text-center mt-10">
                    <v-chip
                        outlined
                        v-show="visibilidadeChip"
                    >
                        Não há dados a serem listados
                    </v-chip>
                </div>
                <div class="text-center mt-10">
                    <v-progress-circular
                        indeterminate
                        color="primary"
                        v-if="!tabelaCarregada"
                    ></v-progress-circular>

                    <v-btn v-show="tabelaCarregada" 
                           x-large width="300" 
                           color="primary" 
                           v-on:click="fecharParcelas">
                           FECHAR
                    </v-btn>
                </div>

            </v-card>
        </div>

        
        <v-snackbar
            v-model="snackbar"
            :timeout="2000"
            color="primary"
            outlined
        >
            O campo de valor é obrigatório.
            <v-btn
                text
                @click="snackbar = false"
                color="primary"
            >
                Fechar
            </v-btn>
        </v-snackbar>
    </div>
</template>

<script>
import http from '../urlConfig'

export default {
  name: 'Inputs',

  components: {
  },
  
  props:{
      instituicoes: Array,
      convenios: Array,
      parcelas: Array
  },
  
  methods:{
      simular(){
          if(this.valor_emprestimo == '' || this.valor_emprestimo == 0 || this.valor_emprestimo == ' '){
              this.snackbar = true;
          }else{
            this.visibilidadeParcelas = !this.visibilidadeParcelas;
            this.visibilidadeInputs = !this.visibilidadeInputs;
            this.postSimular();
          }

      },

      postSimular(){
        
          const params = {
                valor_emprestimo:  parseFloat(this.valor_emprestimo),
                instituicoes: this.instituicoesFilter,
                convenios:  this.conveniosFilter,
                parcela: this.parcela
            }

            http.post('/api/simular', params).then((response)=> {
                if(response.data.BMG){
                    let bmg = response.data.BMG;
                    bmg.forEach(element => {
                        element.instituicao = 'BMG';
                        element.id = this.parcelasCompleta.length;
                        this.parcelasCompleta.push(element);
                    });
                }

                if(response.data.PAN){
                    let pan = response.data.PAN;
                    pan.forEach(element => {
                        element.instituicao = 'PAN';
                        element.id = this.parcelasCompleta.length;
                        this.parcelasCompleta.push(element);
                    });
                }

                if(response.data.OLE){
                    let ole = response.data.OLE;
                    ole.forEach(element => {
                        element.instituicao = 'OLE';
                        element.id = this.parcelasCompleta.length;
                        this.parcelasCompleta.push(element);
                    });
                }
                this.tabelaCarregada = true;

                if(this.parcelasCompleta.length == 0)
                    this.visibilidadeChip = true;
            }).catch(function (error) {
                console.log(error);
            });
      },

      fecharParcelas(){
            this.visibilidadeParcelas = !this.visibilidadeParcelas;
            this.visibilidadeInputs = !this.visibilidadeInputs;
            this.valor_emprestimo = '';
            this.parcela = '';
            this.conveniosFilter = [];
            this.instituicoesFilter = [];
            this.parcelasCompleta = [];
            this.tabelaCarregada = false;
            this.visibilidadeChip = false;
      },

  },

  data: () => ({
      valor_emprestimo: '',
      parcela: '',
      instituicoesFilter: [],
      conveniosFilter: [],
      visibilidadeParcelas: false,
      visibilidadeInputs: true,
      tabelaCarregada: false,
      parcelasCompleta: [],
      snackbar: false,
      visibilidadeChip: false
  }),
};
</script>

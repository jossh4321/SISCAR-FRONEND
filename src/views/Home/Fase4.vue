<template>
  <div>
    <template v-if="showInfo">
      <v-card
        elevation="3"
        outlined
        class="card"
        height="700px"
        style="text-align: center; height: 700px; padding-top: 30px;"
      >
        <div class="progress">
          <v-progress-circular size="80" width="10" color="red" indeterminate></v-progress-circular>
        </div>
        <v-card-title class="justify-center" style="margin-top:20px;color:red">Cargando Datos...</v-card-title>
      </v-card>
    </template>
    <template v-else>
      <v-app-bar color="primary accent-4" dense dark>
        <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
          <span> 
            <v-chip
              class="ma-2"
              color="white"
              label
              text-color="green"
              @click="navegarto('/dashboard/Home')"
            >
              <v-icon left>
                mdi-home
              </v-icon>
              Menú principal
            </v-chip>
            > 
            <v-chip
              class="ma-2"
              color="white"
              label
              text-color="primary"
              @click="navegarto('/dashboard/Tratamiento')"
            >
              <v-icon left>
                mdi-label
              </v-icon>
              Tratamiento
            </v-chip>
            >
            <v-chip
              class="ma-2"
              color="white"
              outlined
              pill
            >
              <v-icon left>
                mdi-label
              </v-icon>
              Fase 4
            </v-chip>
          </span>
        <v-spacer></v-spacer>
      </v-app-bar>

      <v-sheet class="overflow-hidden">
        <!--  style="position: relative;"  -->
        <v-container class="fill-height">
          <v-card class="card">
            <v-card-title>Residentes actuales en la Fase 4</v-card-title>
            <v-container>
              <v-row>
                <v-col xs="12" sm="12" md="12" lg="12" xl="12">
                  <!-- <v-text-field
                        dark
                        flat
                        solo-inverted
                        prepend-icon="search"
                        label="Buscar residente"
                        class="mt-7 textsearch"
                        >
                        flat
                        solo-inverted
                  </v-text-field>-->
                  <v-autocomplete
                    :items="listResidentes"
                    v-model="resi"
                    :search-input.sync="searchResidente"
                    filled
                    chips
                    outlined
                    color="#009900"
                    label="Busque a un residente"
                    item-text="residente"
                    return-object
                  >
                    <template v-slot:selection="data">
                      <v-chip
                        v-bind="data.attrs"
                        :input-value="data.selected"
                        style="margin-top:5px"
                      >
                        <v-avatar left color="#b3b3ff" size="24">
                          <span style="font-size:12px">{{ data.item.residente.charAt(0) }}</span>
                        </v-avatar>
                        {{ data.item.residente }}
                      </v-chip>
                    </template>
                    <template v-slot:item="data">
                      <template>
                        <v-list-item-avatar>
                          <v-avatar left color="#b3b3ff" size="24">
                            <span style="font-size:12px">{{ data.item.residente.charAt(0) }}</span>
                          </v-avatar>
                        </v-list-item-avatar>
                        <v-list-item-content>
                          <v-list-item-title>
                            Nombre completo: {{ data.item.residente }}
                            {{ data.item.apellido }}
                          </v-list-item-title>
                          <v-list-item-subtitle>
                            Nro. Documento:
                            {{ data.item.numeroDocumento }}
                          </v-list-item-subtitle>
                        </v-list-item-content>
                      </template>
                    </template>
                  </v-autocomplete>
                </v-col>
              </v-row>
              <template v-if="!listaCompleta">
                <v-card elevation="3" outlined class="card" style="text-align: center; height: 150px; padding-top: 30px;">
                  <div class="progress">
                    <v-progress-circular size="80" width="10" color="red" indeterminate></v-progress-circular>
                  </div>
                </v-card>
              </template>
              <template v-else-if="listaCompleta && listaResidentes.length == 0">
                <v-alert
                  text
                  outlined
                  border="left"
                  color="deep-orange"
                  width="97%"
                  class="ml-3"
                  icon="info"
                >
                  No se ha encontrado residentes en esta fase
                </v-alert>
              </template>
              <template v-else>
                <v-row>
                  <v-spacer></v-spacer>
                  <v-chip
                    color="success"
                    dark
                  >
                    Se han encontrado {{ listaResidentes.length }} residentes
                  </v-chip>
                  <v-spacer></v-spacer>
                </v-row>
                <v-data-iterator
                  :items="listaResidentes"
                  :items-per-page.sync="itemsPerPage"
                  hide-default-footer
                  :page="page"
                >
                  <template v-slot:default="props">
                    <v-row>
                      <v-col
                        v-for="residente in props.items"
                        :key="residente.id"
                        xs="12"
                        sm="6"
                        md="6"
                        lg="4"
                        xl="4"
                      >
                        <v-hover v-slot="{ hover }">
                          <v-card
                            class="pa-4 mx-auto"
                            :class="{ 'on-hover': hover }"
                            max-width="90%"
                            @click.native="abrirProgresoFase4(residente.id)"
                            style="cursor: pointer"
                          >
                            <v-img
                              src="../../assets/static/residente_hombre.png"
                              max-width="90%"
                              max-height="250"
                              class="mx-auto"
                              v-if="residente.sexo == 'Masculino'"
                              style="margin-left: 20%!important; margin-right: 20%!important;"
                            ></v-img>
                            <v-img
                              src="../../assets/static/residente_mujer.png"
                              max-width="90%"
                              max-height="250"
                              class="mx-auto"
                              v-else-if="residente.sexo == 'Femenino'"
                              style="margin-left: 20%!important; margin-right: 20%!important;"
                            ></v-img>

                            <v-card-title
                              class="justify-center"
                              style="font-size: 13px;text-align: center;word-break: normal; padding-bottom: 0;"
                            >{{residente.nombre}} {{residente.apellido}}</v-card-title>
                            <v-card-title
                              class="justify-center"
                              style="font-size: 10px;text-align: center;word-break: normal; padding: 0;"
                            >{{residente.tipoDocumento}} - {{residente.numeroDocumento}}</v-card-title>
                            <v-btn block class="my-button" color="success">
                              <v-icon left dark>mdi-cloud-upload</v-icon>Ver progreso
                            </v-btn>
                          </v-card>
                        </v-hover>
                      </v-col>
                    </v-row>
                  </template>
                  <template v-slot:footer>
                    <v-row
                      class="mt-2"
                      align="center"
                      justify="center"
                      style="padding-left: 20px"
                    >
                      <span class="grey--text">Residentes por página</span>
                      <v-menu offset-y>
                        <template v-slot:activator="{ on, attrs }">
                          <v-btn
                            dark
                            text
                            color="green"
                            class="ml-2"
                            v-bind="attrs"
                            v-on="on"
                          >
                            {{ itemsPerPage }}
                            <v-icon>mdi-chevron-down</v-icon>
                          </v-btn>
                        </template>
                        <v-list>
                          <v-list-item
                            v-for="(number, index) in itemsPerPageArray"
                            :key="index"
                            @click="updateItemsPerPage(number)"
                          >
                            <v-list-item-title>{{ number }}</v-list-item-title>
                          </v-list-item>
                        </v-list>
                      </v-menu>

                      <v-spacer></v-spacer>

                      <div style="padding-right: 20px">
                        <span
                          class="mr-4
                          grey--text"
                        >
                          Página {{ page }} de {{ numberOfPages }}
                        </span>
                        <v-btn
                          fab
                          dark
                          color="green"
                          class="mr-1"
                          @click="formerPage"
                        >
                          <v-icon>mdi-chevron-left</v-icon>
                        </v-btn>
                        <v-btn
                          fab
                          dark
                          color="green"
                          class="ml-1"
                          @click="nextPage"
                        >
                          <v-icon>mdi-chevron-right</v-icon>
                        </v-btn>
                      </div>
                    </v-row>
                  </template>
                </v-data-iterator>
              </template>
            </v-container>
          </v-card>
        </v-container>

        <v-navigation-drawer v-model="drawer" absolute temporary>
          <v-list-item>
            <v-list-item-content>
              <v-list-item-title>Acerca de la Fase 4</v-list-item-title>
            </v-list-item-content>
          </v-list-item>

          <v-divider></v-divider>

          <v-list dense>
            <v-list-item link>
              <v-list-item-icon>
                <v-icon>mdi-content-save-all-outline</v-icon>
              </v-list-item-icon>

              <v-list-item-content>
                <v-list-item-title>Gestionar Talleres</v-list-item-title>
              </v-list-item-content>
            </v-list-item>
          </v-list>
        </v-navigation-drawer>
      </v-sheet>
    </template>
  </div>
</template>

<script>
import axios from "axios";

export default {
  components: {},
  data() {
    return {
      itemsPerPageArray: [6, 12, 18],
      page: 1,
      itemsPerPage: 6,
      showInfo: true,
      drawer: false,
      group: null,
      isfiltrado: false,
      listaCompleta: true,
      searchResidente: null,
      listaResidentes: [],
      listResidentes: [],
      resi: {}
    };
  },
  watch: {
    group() {
      this.drawer = false;
    },
    searchResidente(value) {
      if (value == null) {
        this.residente = {
          residente: "",
          id: "",
          numeroDocumento: ""
        };
      }

      if (this.listResidentes.length > 0) {
        return;
      }
      if (this.loadingSearch) {
        return;
      }

      this.loadingSearch = true;

      axios
        .get("/residente/all/fase/4")
        .then(res => {
          let residentesMap = res.data.map(function(res) {
            return {
              residente: res.nombre + " " + res.apellido,
              id: res.id,
              numeroDocumento: res.tipoDocumento + ": " + res.numeroDocumento
            };
          });

          this.listResidentes = residentesMap;

          this.loadingSearch = false;
        })
        .catch(error => {
          console.error(error);
        });
    },
    resi(ahora, antes) {
      this.listaCompleta = false;
      if (ahora === undefined) {
        axios
          .get("/residente/all/fase/4")
          .then(res => {
            this.listaResidentes = res.data;
            this.isfiltrado = false;
            this.listaCompleta = true;
            //this.loadingSearch = false;
          })
          .catch(error => {
            console.error(error);
          });
      } else {
        if (this.isfiltrado) {
          axios
            .get("/residente/all/fase/4")
            .then(res => {
              //para los cards
              this.listaResidentes = res.data;
              this.listaResidentes = this.listaResidentes.filter(
                el => el.id == ahora.id
              );
              this.isfiltrado = true;
              this.listaCompleta = true;
            })
            .catch(error => {
              console.error(error);
            });
        } else {
          this.listaResidentes = this.listaResidentes.filter(
            el => el.id == ahora.id
          );
          this.isfiltrado = true;
          this.listaCompleta = true;
        }
      }
    }
  },
  async created() {
    var listado = [];
    await axios
      .get("/residente/all/fase/4")
      .then(res => {
        //para los cards
        this.listaResidentes = res.data;
        listado = this.listaResidentes;
        let residentesMap = listado.map(function(res) {
          return {
            residente: res.nombre + " " + res.apellido,
            id: res.id,
            numeroDocumento: res.tipoDocumento + ": " + res.numeroDocumento
          };
        });

        this.listResidentes = residentesMap;
        this.showInfo = false;
        //this.loadingSearch = false;
      })
      .catch(error => {
        console.error(error);
      });
  },
  methods: {
    toogleDrawer() {
      this.$store.commit("toggleDrawer");
    },
    abrirProgresoFase4(id) {
      var rutacompleta = "/dashboard/ProgresoF4Residente/" + id;
      this.$router.push(rutacompleta);
    },
    navegarto(ruta){
      this.$router.push(ruta)
    },
    nextPage () {
        if (this.page + 1 <= this.numberOfPages) this.page += 1
      },
      formerPage () {
        if (this.page - 1 >= 1) this.page -= 1
      },
      updateItemsPerPage (number) {
        this.itemsPerPage = number
        if(this.page > 1) {
          this.page = 1
        }
      },
  },

  computed: {
    numberOfPages () {
        return Math.ceil(this.listaResidentes.length / this.itemsPerPage)
      },
  }
};
</script>
<style scoped>
.on-hover {
  background-color: white;
}
.on-hover:hover {
   box-shadow: 5px 5px 10px 0px rgba(0, 0, 0, 0.5);
    transition: height 0.3s, box-shadow 0.3s;
    transform-origin: center center;
    animation: scale 300ms ease-in-out forwards;
    background-color: #d3f1ec;
    transition: background-color 300ms ease-in-out;
}
.textsearch {
  background-color: #ff695b;
}
@keyframes scale {
  to {
    transform: scale(1.01);
  }
}
</style>
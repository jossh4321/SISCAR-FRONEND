<template>
  <div>
    <v-card class="card">
      <v-card-title> Gestionar Informes </v-card-title>
      <v-data-table
        :headers="headers"
        :items="informes"
        :search="search"
        class="elevation-1"
        :loading="loading"
        loading-text="Cargando informes"
      >
        <template v-slot:top>
          <v-toolbar flat>
            <v-toolbar-title>Informes de las Usuarias CAR</v-toolbar-title>
            <v-divider class="mx-4" inset vertical></v-divider>
            <v-spacer></v-spacer>
            <v-text-field
              v-model="search"
              append-icon="mdi-magnify"
              label="Search"
              single-line
              hide-details
            ></v-text-field>
             <v-col cols="12" sm="6" md="4">
              <v-dialog ref="dialog" v-model="modal" persistent width="290px">
                <template v-slot:activator="{ on, attrs }">
                  <v-text-field
                    v-model="dates"
                    label="Rango de fechas"
                    prepend-icon="mdi-calendar"
                    readonly
                    single-line
                    v-bind="attrs"
                    v-on="on"
                    hide-details
                  ></v-text-field>
                </template>
                <v-date-picker v-model="dates" locale="es-es" range scrollable>
                  <v-spacer></v-spacer>
                  <v-btn text color="primary" @click="modal = false">
                    Cancel
                  </v-btn>
                  <v-btn text color="primary" @click="cargarDocumentosRango(dates)">
                    OK
                  </v-btn>
                </v-date-picker>
              </v-dialog>
            </v-col>
            <v-spacer></v-spacer>
            <!--Dialogo de Registro-->
            <v-dialog persistent v-model="dialogoregistro" max-width="880px">
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  color="success"
                  dark
                  class="mb-2"
                  v-bind="attrs"
                  v-on="on"
                >
                  <v-icon left>mdi-plus</v-icon>
                  <span>Registrar Informe</span>
                </v-btn>
              </template>
              <SeleccionarInforme
                :listaresidentes="listaresidentes"
                @close-dialog-save="closeDialogRegistrar()"
              ></SeleccionarInforme>
            </v-dialog>
            <!---->
          </v-toolbar>
        </template>
        <template v-slot:[`item.actions`]="{ item }">
          <v-row align="center" justify="space-around">
            <v-btn
              color="warning"
              dark
              @click="abrirDialogoActualizar(item.id, item.tipo)"
            >
              <v-icon left>mdi-briefcase-edit</v-icon>
              <span>Actualizar</span>
            </v-btn>
            <v-btn
              color="info"
              dark
              @click="abrirDialogoDetalle(item.id, item.tipo)"
            >
              <v-icon left>mdi-file-eye</v-icon>
              <span>Detalle</span>
            </v-btn>
          </v-row>
        </template>
      </v-data-table>
      <v-dialog persistent v-model="dialogoIEIactualizacion" max-width="880px">
        <ActualizarInformeEducativoInicial
          v-if="dialogoIEIactualizacion"
          :informe="informe"
          :listaresidentes="listaresidentes"
          @close-dialog-update="closeDialogActualizar()"
        >
        </ActualizarInformeEducativoInicial>
      </v-dialog>
      <v-dialog persistent v-model="dialogoISIactualizacion" max-width="880px">
        <ActualizarInformeSocialInicial
          v-if="dialogoISIactualizacion"
          :listaresidentes="listaresidentes"
          :informe="informe"
          @close-dialog-update="closeDialogActualizar()"
        >
        </ActualizarInformeSocialInicial>
      </v-dialog>
      <v-dialog persistent v-model="dialogoIPIactualizacion" max-width="880px">
        <ActualizarInformePsicologicoInicial
          v-if="dialogoIPIactualizacion"
          :informe="informe"
          :listaresidentes="listaresidentes"
          @close-dialog-update="closeDialogActualizar()"
        >
        </ActualizarInformePsicologicoInicial>
      </v-dialog>
      <v-dialog persistent v-model="dialogoIEEactualizacion" max-width="880px">
        <ActualizarInformeEducativoEvolutivo
          v-if="dialogoIEEactualizacion"
          :informe="informe"
          :titulo="titulo"
          :listaresidentes="listaresidentes"
          @close-dialog-update="closeDialogActualizar()"
        >
        </ActualizarInformeEducativoEvolutivo>
      </v-dialog>
      <v-dialog persistent v-model="dialogoISEactualizacion" max-width="880px">
        <ActualizarInformeSocialEvolutivo
          v-if="dialogoISEactualizacion"
          :informe="informe"
          :titulo="titulo"
          :listaresidentes="listaresidentes"
          @close-dialog-update="closeDialogActualizar()"
        >
        </ActualizarInformeSocialEvolutivo>
      </v-dialog>
      <v-dialog persistent v-model="dialogoIPEactualizacion" max-width="880px">
        <ActualizarInformePsicologicoEvolutivo
          v-if="dialogoIPEactualizacion"
          :informe="informe"
          :titulo="titulo"
          :listaresidentes="listaresidentes"
          @close-dialog-update="closeDialogActualizar()"
        >
        </ActualizarInformePsicologicoEvolutivo>
      </v-dialog>
      <v-dialog persistent v-model="dialogoIEIdetalle" max-width="880px">
        <DetalleInformeEducativoInicial
          v-if="dialogoIEIdetalle"
          :informe="informe"
          @close-dialog-detail="closeDialogDetalle()"
        >
        </DetalleInformeEducativoInicial>
      </v-dialog>
      <v-dialog persistent v-model="dialogoIEEdetalle" max-width="880px">
        <DetalleInformeEducativoEvolutivo
          v-if="dialogoIEEdetalle"
          :informe="informe"
          :titulo="titulo"
          @close-dialog-detail="closeDialogDetalle()"
        >
        </DetalleInformeEducativoEvolutivo>
      </v-dialog>
      <v-dialog persistent v-model="dialogoISIdetalle" max-width="880px">
        <DetalleInformeSocialInicial
          :informe="informe"
          v-if="dialogoISIdetalle"
          @close-dialog-detail="closeDialogDetalle()"
        >
        </DetalleInformeSocialInicial>
      </v-dialog>
      <v-dialog persistent v-model="dialogoISEdetalle" max-width="880px">
        <DetalleInformeSocialEvolutivo
          v-if="dialogoISEdetalle"
          :informe="informe"
          :titulo="titulo"
          @close-dialog-detail="closeDialogDetalle()"
        >
        </DetalleInformeSocialEvolutivo>
      </v-dialog>
      <v-dialog persistent v-model="dialogoIPIdetalle" max-width="880px">
        <DetalleInformePsicologicoInicial
          v-if="dialogoIPIdetalle"
          :informe="informe"
          @close-dialog-detail="closeDialogDetalle()"
        >
        </DetalleInformePsicologicoInicial>
      </v-dialog>
      <v-dialog persistent v-model="dialogoIPEdetalle" max-width="880px">
        <DetalleInformePsicologicoEvolutivo
          v-if="dialogoIPEdetalle"
          :informe="informe"
          :titulo="titulo"
          @close-dialog-detail="closeDialogDetalle()"
        >
        </DetalleInformePsicologicoEvolutivo>
      </v-dialog>
      <!----->
    </v-card>
  </div>
</template>

<script>
import axios from "axios";
import SeleccionarInforme from "@/components/informes/SeleccionarInforme.vue";
import ActualizarInformeEducativoInicial from "@/components/informes/ActualizarInformeEducativoInicial.vue";
import ActualizarInformeEducativoEvolutivo from "@/components/informes/ActualizarInformeEducativoEvolutivo.vue";
import ActualizarInformeSocialInicial from "@/components/informes/ActualizarInformeSocialInicial.vue";
import ActualizarInformeSocialEvolutivo from "@/components/informes/ActualizarInformeSocialEvolutivo.vue";
import ActualizarInformePsicologicoInicial from "@/components/informes/ActualizarInformePsicologicoInicial.vue";
import ActualizarInformePsicologicoEvolutivo from "@/components/informes/ActualizarInformePsicologicoEvolutivo.vue";
import DetalleInformeEducativoInicial from "@/components/informes/DetalleInformeEducativoInicial.vue";
import DetalleInformeEducativoEvolutivo from "@/components/informes/DetalleInformeEducativoEvolutivo.vue";
import DetalleInformeSocialInicial from "@/components/informes/DetalleInformeSocialInicial.vue";
import DetalleInformeSocialEvolutivo from "@/components/informes/DetalleInformeSocialEvolutivo.vue";
import DetalleInformePsicologicoInicial from "@/components/informes/DetalleInformePsicologicoInicial.vue";
import DetalleInformePsicologicoEvolutivo from "@/components/informes/DetalleInformePsicologicoEvolutivo.vue";
import { mapMutations, mapState } from "vuex";
export default {
  name: "GestionarInforme",
  components: {
    SeleccionarInforme,
    ActualizarInformeEducativoInicial,
    ActualizarInformeEducativoEvolutivo,
    ActualizarInformeSocialInicial,
    ActualizarInformeSocialEvolutivo,
    ActualizarInformePsicologicoInicial,
    ActualizarInformePsicologicoEvolutivo,
    DetalleInformeEducativoInicial,
    DetalleInformeEducativoEvolutivo,
    DetalleInformeSocialInicial,
    DetalleInformeSocialEvolutivo,
    DetalleInformePsicologicoInicial,
    DetalleInformePsicologicoEvolutivo,
  },
  data() {
    return {
      search: "",
      informe: {},
      headers: [
        {
          text: "Código del Informe",
          align: "start",
          sortable: false,
          value: "codigodocumento",
        },
        { text: "Nombre del residente", value: "nombrecompleto" },
        { text: "Fecha de creación", value: "fechacreacion" },
        { text: "Tipo de Informe", value: "tipo" },
        { text: "Acciones", value: "actions", sortable: false },
      ],
      listaresidentes: [],
      titulo: "Titulo por defecto",
      /*listaeducadores: [],
      listasociales: [],
      listapsicologos: [],*/
      dialogoregistro: false,
      dialogoIEIactualizacion: false,
      dialogoIEEactualizacion: false,
      dialogoISIactualizacion: false,
      dialogoISEactualizacion: false,
      dialogoIPIactualizacion: false,
      dialogoIPEactualizacion: false,
      dialogoIEIdetalle: false,
      dialogoIEEdetalle: false,
      dialogoISIdetalle: false,
      dialogoISEdetalle: false,
      dialogoIPIdetalle: false,
      dialogoIPEdetalle: false,
      listaresidentes: [],      
      loading: true,
      faseEducativaInicial: {
        fase: "1",
        area: "educativa",
        documentos: ["FichaEducativaIngreso"]
      },
      fromDate: null,
      toDate: null,
      dates: [],
      modal: false,
    };
  },
  async created() {
    this.obtenerInformes();
    this.obtenerResidentes();
    //this.obtenerEducadores();
    //this.obtenerSociales();
    //this.obtenerPsicologos();    
  },
  methods: {
    ...mapMutations(["setInformes"]),
    closeDialogRegistrar() {
      this.dialogoregistro = false;
    },
    closeDialogActualizar() {
      this.dialogoIEIactualizacion = false;
      this.dialogoIEEactualizacion = false;
      this.dialogoISEactualizacion = false;
      this.dialogoISIactualizacion = false;
      this.dialogoIPIactualizacion = false;
      this.dialogoIPEactualizacion = false;
    },
    closeDialogDetalle() {
      this.dialogoIEIdetalle = false;
      this.dialogoIEEdetalle = false;
      this.dialogoISIdetalle = false;
      this.dialogoISEdetalle = false;
      this.dialogoIPIdetalle = false;
      this.dialogoIPEdetalle = false;
    },
    async obtenerInformes() {

      let listParams = [];
      
      let fromDateParam = this.fromDate == null ? "": "FromDate=" + this.fromDate;
      let toDateParam = this.toDate == null ? "": "ToDate=" + this.toDate;

      if(fromDateParam != "") {

        listParams.push(fromDateParam);

      }
      
      if(toDateParam != "") {

        listParams.push(toDateParam);

      }

      let params = listParams.join("&");

      await axios
        .get("/informe/all?" + params)
        .then((res) => {
          this.loading = false;
          var info = {};
          info = res.data;
          for (var x = 0; x < res.data.length; x++) {
            info[x].fechacreacion = res.data[x].fechacreacion.split("T")[0];
            info[x].tipo = res.data[x].tipo.replace(/([a-z])([A-Z])/g, "$1 $2");
          }
          console.log(res.data[1].tipo);
          this.setInformes(info);
        })
        .catch((err) => console.log(err));
    },
    async abrirDialogoActualizar(idinforme, tipo) {
      console.log("El resultado de esta cagada es:" + idinforme + "  " + tipo);
      this.informe = await this.loadInformeModificacion(idinforme);
      switch (tipo) {
        case "Informe Educativo Inicial":
          this.dialogoIEIactualizacion = !this.dialogoIEIactualizacion;
          break;
        case "Informe Educativo Evolutivo":
          this.titulo = "Modificar Informe Educativo Evolutivo";
          this.dialogoIEEactualizacion = !this.dialogoIEEactualizacion;
          break;
        case "Informe Educativo Final":
          this.titulo = "Modificar Informe Educativo Final";
          this.dialogoIEEactualizacion = !this.dialogoIEEactualizacion;
          break;
        case "Informe Social Inicial":
          this.dialogoISIactualizacion = !this.dialogoISIactualizacion;
          break;
        case "Informe Social Evolutivo":
          this.titulo = "Modificar Informe Social Evolutivo";
          this.dialogoISEactualizacion = !this.dialogoISEactualizacion;
          break;
        case "Informe Social Final":
          this.titulo = "Modificar Informe Social Final";
          this.dialogoISEactualizacion = !this.dialogoISEactualizacion;
          break;
        case "Informe Psicologico Inicial":
          this.dialogoIPIactualizacion = !this.dialogoIPIactualizacion;
          break;
        case "Informe Psicologico Evolutivo":
          this.titulo = "Modificar Informe Psicologico Evolutivo";
          this.dialogoIPEactualizacion = !this.dialogoIPEactualizacion;
          break;
        case "Informe Psicologico Final":
          this.titulo = "Modificar Informe Psicologico Final";
          this.dialogoIPEactualizacion = !this.dialogoIPEactualizacion;
          break;
        default:
          console.log("Ayuda mi codigo no funciona :c");
      }
    },
    async abrirDialogoDetalle(idinforme, tipo) {
      this.informe = await this.loadInformeModificacion(idinforme);

      switch (tipo) {
        case "Informe Educativo Inicial":
          this.dialogoIEIdetalle = !this.dialogoIEIdetalle;
          break;
        case "Informe Educativo Evolutivo":
          this.titulo = "Detalle del Informe Educativo Evolutivo";
          this.dialogoIEEdetalle = !this.dialogoIEEdetalle;
          break;
        case "Informe Educativo Final":
          this.titulo = "Detalle del Informe Educativo Final";
          this.dialogoIEEdetalle = !this.dialogoIEEdetalle;
          break;
        case "Informe Social Inicial":
          this.titulo = "Detalle del Informe Social Inicial";
          this.dialogoISIdetalle = !this.dialogoISIdetalle;
          break;
        case "Informe Social Evolutivo":
          this.titulo = "Detalle del Informe Social Evolutivo";
          this.dialogoISEdetalle = !this.dialogoISEdetalle;
          break;
        case "Informe Social Final":
          this.titulo = "Detalle del Informe Social Final";
          this.dialogoISEdetalle = !this.dialogoISEdetalle;
          break;
        case "Informe Psicologico Inicial":
          this.dialogoIPIdetalle = !this.dialogoIPIdetalle;
          break;
        case "Informe Psicologico Evolutivo":
          this.titulo = "Detalle del Informe Psicologico Evolutivo";
          this.dialogoIPEdetalle = !this.dialogoIPEdetalle;
          break;
        case "Informe Psicologico Final":
          this.titulo = "Detalle del Informe Psicologico Final";
          this.dialogoIPEdetalle = !this.dialogoIPEdetalle;
          break;
        default:
          console.log("Ayuda mi codigo no funciona :c");
      }
    },
    async loadInformeModificacion(idinforme) {
      var info = {};
      await axios
        .get("/informe/id?id=" + idinforme)
        .then((res) => {
          info = res.data;
          info.fechacreacion = res.data.fechacreacion.split("T")[0];
        })
        .catch((err) => console.log(err));
      return info;
    },
    async obtenerResidentes() {
      await axios
        .get("/residente/all")
        .then((x) => {
          this.listaresidentes = x.data;
          console.log(this.listaresidentes);
        })
        .catch((err) => console.log(err));
    },   
    cargarDocumentosRango(dates) {
      this.dates = dates.sort();          
      this.fromDate = this.formatDate(dates[0]);
      this.toDate = this.formatDate(dates[1]);      
      this.obtenerInformes();      
      this.modal = false;
    },
    formatDate (date) {
        if (!date) return null;
        const [year, month, day] = date.split('-')
        return `${month}-${day}-${year}`
    },
  },
  computed: {
    ...mapState(["informes"]),
    dateRangeText() {
      return this.dates.join(" ~ ");
    },
  },
};
</script>
<style scoped>
.card {
  margin: 20px;
}
</style>

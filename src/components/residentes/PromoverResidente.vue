<template>
  <v-card>
    <v-card-title class="justify-center"
      >Promover Residente:
      <span style="margin-left:5px;font-weight:bold">{{
        residente.nombre + " " + residente.apellido
      }}</span></v-card-title
    >
    <v-card style="padding: 15px 20px">
      <div class="container-user">
        <v-card v-if="evitarPromocion()" color="#FFDFDF">
          <v-alert
            dense
            outlined
            type="error"
            style="font-weight:bold;font-size:16px;"
          >
            No se puede promover al residente porque sus documentos de la fase
            actual no han sido completados.
          </v-alert>
        </v-card>
        <v-expansion-panels focusable>
          <v-expansion-panel no-gutters>
            <v-card style="border: 1px solid #c1c1c1">
              <v-expansion-panel-header
                >Datos Referenciales del residente</v-expansion-panel-header
              >
              <v-expansion-panel-content>
                <form style="margin-top:15px">
                  <v-row no-gutters>
                    <v-col>
                      <v-text-field
                        style="margin-left:7%"
                        v-model="residente.nombre"
                        label="Nombres"
                        readonly
                        color="#009900"
                      ></v-text-field>
                    </v-col>
                    <v-col>
                      <v-text-field
                        style="margin-left:7%"
                        v-model="residente.apellido"
                        label="Apellidos"
                        readonly
                        color="#009900"
                      ></v-text-field>
                    </v-col>
                    <v-col>
                      <v-text-field
                        style="margin-left:7%"
                        v-model="
                          residente.progreso[residente.progreso.length - 1].fase
                        "
                        label="Fase actual"
                        readonly
                        color="#009900"
                      ></v-text-field>
                    </v-col>
                  </v-row>
                  <v-row no-gutters>
                    <v-col>
                      <v-text-field
                        style="margin-left:7%"
                        v-model="residente.sexo"
                        label="Sexo"
                        readonly
                        color="#009900"
                      ></v-text-field>
                    </v-col>
                    <v-col>
                      <v-menu
                        v-model="datemenu"
                        :close-on-content-click="false"
                        :nudge-right="40"
                        transition="scale-transition"
                        offset-y
                        min-width="290px"
                      >
                        <template v-slot:activator="{ on, attrs }">
                          <v-text-field
                            style="margin-left:7%"
                            v-model="residente.fechaNacimiento"
                            label="Fecha de Nacimiento"
                            prepend-icon="mdi-calendar"
                            readonly
                            v-bind="attrs"
                            v-on="on"
                            color="#009900"
                          ></v-text-field>
                        </template>
                        <v-date-picker
                          v-model="residente.fechaNacimiento"
                          readonly
                          @input="menu2 = false"
                          locale="es-es"
                        ></v-date-picker>
                      </v-menu>
                    </v-col>
                    <v-col>
                      <v-select
                        style="margin-top:8%;margin-left:7%"
                        :items="[
                          'DNI',
                          'Pasaporte',
                          'Carnet Extranjeria',
                          'dni',
                        ]"
                        label="Tipo de Documento"
                        dense
                        readonly
                        color="#009900"
                        v-model="residente.tipoDocumento"
                      ></v-select>
                    </v-col>
                  </v-row>
                  <v-row no-gutters>
                    <v-col cols="4">
                      <v-text-field
                        style="margin-left:7%"
                        v-model="residente.numeroDocumento"
                        label="Numero de Documento"
                        readonly
                        color="#009900"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="8">
                      <v-text-field
                        style="margin-left:7%"
                        v-model="residente.lugarNacimiento"
                        label="Lugar de Nacimiento"
                        readonly
                        color="#009900"
                      ></v-text-field>
                    </v-col>
                  </v-row>
                  <v-btn
                    color="info"
                    dark
                    @click="abrirDialogoDetalleFase(residente.id)"
                  >
                    <v-icon left> info </v-icon>
                    <span>Visualizar</span>
                  </v-btn>
                </form>
              </v-expansion-panel-content>
            </v-card>
          </v-expansion-panel>
        </v-expansion-panels>
        <v-card style="padding:1%" elevation="0">
          <form>
            <v-text-field
              v-model="progresoFase.documentotransicion.observaciones"
              style="margin-top:2%"
              label="Observaciones"
              color="#009900"
              @input="
                $v.progresoFase.documentotransicion.observaciones.$touch()
              "
              @blur="$v.progresoFase.documentotransicion.observaciones.$touch()"
              :error-messages="errorObservaciones"
            ></v-text-field>
            <v-select
              v-model="progresoFase.fase"
              :items="seleccionItems()"
              item-text="text"
              item-value="value"
              label="Seleccionar la fase a promover"
              dense
              color="#009900"
              @input="$v.progresoFase.fase.$touch()"
              @blur="$v.progresoFase.fase.$touch()"
              :error-messages="errorSeleccionFase"
            ></v-select>
            <v-card
              style="margin-top:10px;padding:5px 5px;background-color:#EAEAEA"
            >
              <v-card-actions>Agregar Firma del Residente</v-card-actions>
              <v-row>
                <v-col :cols="12" align="left">
                  <div style="padding:1%">
                    <vue-dropzone
                      ref="myVueDropzone"
                      @vdropzone-success="afterSuccess2"
                      @vdropzone-removed-file="afterRemoved2"
                      id="dropzone2"
                      :options="dropzoneOptions2"
                    >
                    </vue-dropzone>
                  </div>
                  <v-card v-if="errorUrlFirma" color="red">
                    <v-card-text class="text-center" style="color: white">
                      Debe Subir una imagen de la firma obligatoriamente
                    </v-card-text>
                  </v-card>
                </v-col>
              </v-row>
              <v-card-actions>
                <v-btn dark color="blue" @click="verFirma()">
                  <v-icon dark>
                    mdi-draw
                  </v-icon>
                  <span style="margin-left:2%">Visualizar Firma</span>
                </v-btn>
              </v-card-actions>
            </v-card>
            <v-card-actions style="margin-top:5%">
              <v-btn
                dark
                color="success"
                @click="registrarDocumentoTransicionFase()"
              >
                <span>Registrar</span>
              </v-btn>
              <v-btn color="red" dark @click="cerrarDialogo()">
                Cerrar
              </v-btn>
            </v-card-actions>
          </form>
        </v-card>
      </div>
    </v-card>
    <v-dialog v-model="dialogVistaPreviaFirma" persistent max-width="600px">
      <v-card align="center">
        <v-card-title>
          <span class="headline">Vista previa</span>
        </v-card-title>
        <v-card-text v-if="imagen != ''">
          <img
            v-if="imagen.includes('http')"
            width="100%"
            height="100%"
            :src="imagen"
            alt=""
          />
          <img
            v-else
            width="100%"
            height="100%"
            :src="'data:image/jpeg;base64,' + imagen"
            alt=""
          />
        </v-card-text>
        <v-card-text v-else>
          <v-card-text style="font-size:18px"
            >No tiene firma registrada</v-card-text
          >
        </v-card-text>
        <div>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" text @click="cerrarVistaPreviaFirma()">
              Cerrar
            </v-btn>
          </v-card-actions>
        </div>
      </v-card>
    </v-dialog>
    <v-dialog persistent v-model="dialogofase" max-width="1000px">
      <FasesResidente
        :datosfase="datosfase"
        :nombreResidente="residente.nombre + ' ' + residente.apellido"
        :dialogofase="dialogofase"
        @close-dialog-fases="closeDialogDetalle()"
      ></FasesResidente>
    </v-dialog>
  </v-card>
</template>

<script>
const m = moment();
import axios from "axios";
import vue2Dropzone from "vue2-dropzone";
import "vue2-dropzone/dist/vue2Dropzone.min.css";
import FasesResidente from "@/components/residentes/FasesResidente.vue";
import { mapMutations, mapState, mapGetters } from "vuex";
import { required, minLength, email, helpers } from "vuelidate/lib/validators";
import moment from "moment";
function esParrafo(value) {
  return /^[A-Za-z\d\s.,;°"“()áéíóúÁÉÍÓÚñÑ]+$/.test(value);
}
export default {
  name: "PromoverResidente",
  props: ["residente", "dialogopromover"],
  components: {
    vueDropzone: vue2Dropzone,
    FasesResidente,
  },
  data() {
    return {
      items1: [
        { value: 2, text: "Fase 2" },
        { value: 3, text: "Fase 3" },
      ],
      items2: [{ value: 3, text: "Fase 3" }],
      activado: false,
      switchPromocion: false,
      progresoFase: {
        educativa: {
          documentos: [],
          estado: "incompleto",
        },
        social: {
          documentos: [],
          estado: "incompleto",
        },
        psicologica: {
          documentos: [],
          estado: "incompleto",
        },
        fase: "",
        documentotransicion: {
          fecha: moment().format("L"),
          idcreador: "",
          observaciones: "",
          firma: {
            urlfirma: "",
            nombre: "",
            cargo: "",
          },
        },
      },
      progresoResidente: {
        fase: "",
        nombre: "",
        fechaingreso: moment().format("L"),
        estado: "",
      },
      dropzoneOptions2: {
        url: "https://httpbin.org/post",
        thumbnailWidth: 250,
        maxFilesize: 5.0,
        maxFiles: 1,
        acceptedFiles: ".png",
        headers: { "My-Awesome-Header": "header value" },
        addRemoveLinks: true,
        dictDefaultMessage:
          "Seleccione la imagen de la firma su dispositivo o arrástrela aquí",
      },
      imagen: "",
      datosfase: null,
      dialogofase: false,
      dialogVistaPreviaFirma: false,
      dialog: false,
      dialog1: false,
      datemenu: false,
      datemenu: false, ///fecha de nacimiento
      datemenu1: false, ///fecha ingreso
      datemenu2: false, ///MODAL fecha ingreso
      datemenu3: false, ///MODAL fecha finalizacion
      step: 1,
    };
  },
  watch: {
    dialogopromover: async function(dialogopromover) {
      if (dialogopromover) {
        this.datosfase = await this.obtenerFase(this.residente.id);
        console.log("datos fase obtenidos al volver a entrar");
        console.log(this.datosfase);
      }
    },
  },
  async created() {
    this.datosfase = await this.obtenerFase(this.residente.id);
    console.log("datos fase obtenidos al crear");
    console.log(this.datosfase);
  },
  methods: {
    ...mapMutations(["replaceResidente"]),
    moment: function() {
      return moment();
    },
    async mensaje(icono, titulo, texto, footer) {
      await this.$swal({
        icon: icono,
        title: titulo,
        text: texto,
        footer: footer,
      });
    },
    evitarPromocion() {
      if (this.datosfase !== null) {
        console.log(this.datosfase);
        if(this.datosfase.progreso[this.datosfase.progreso.length - 1].educativa.estado == "completo" && 
        this.datosfase.progreso[this.datosfase.progreso.length - 1].social.estado == "completo" &&
        this.datosfase.progreso[this.datosfase.progreso.length - 1].psicologica.estado == "completo"){
          this.switchPromocion = false;
          return false;
        }else{
          this.switchPromocion = true;
          return true;
        }
        /*for (let j = 0; j < this.datosfase.progreso[this.datosfase.progreso.length - 1].educativa.documentos.length;j++) {
          if (this.datosfase.progreso[this.datosfase.progreso.length - 1].educativa.documentos[j].estado !== "Completo") {
            this.switchPromocion = true;
            return true;
          }
          console.log("ESTADO:");
          console.log(
            this.datosfase.progreso[this.datosfase.progreso.length - 1]
              .educativa.documentos[j].estado
          );
        }
        this.switchPromocion = false;
        return false;*/
      }
    },
    closeDialogDetalle() {
      this.dialogofase = false;
    },
    cerrarDialogo() {
      this.$emit("close-dialog-promover");
      this.limpiar();
    },
    convertDateFormat(string) {
      console.log(string);
      var date = string.split("/");
      return date[2] + "-" + date[1] + "-" + date[0];
    },
    seleccionItems() {
      var fase = this.residente.progreso[this.residente.progreso.length - 1]
        .fase;
      if (fase === 1) {
        return this.items1;
      } else {
        return this.items2;
      }
    },
    verFirma() {
      this.imagen = this.progresoFase.documentotransicion.firma.urlfirma;
      this.dialogVistaPreviaFirma = true;
    },
    cerrarVistaPreviaFirma() {
      this.dialogVistaPreviaFirma = false;
    },
    afterSuccess2(file, response) {
      // this.urlfirma = file.dataURL.split(",")[1];
      // console.log(this.urlfirma);
      this.progresoFase.documentotransicion.firma.urlfirma = file.dataURL.split(
        ","
      )[1];
      console.log("urlfirma llenada");
      //this.botonCambiarFirma = false;
    },
    afterRemoved2(file, error, xhr) {
      // this.urlfirma = "";
      this.progresoFase.documentotransicion.firma.urlfirma = "";
    },
    calculoFin() {
      var fecha = "";
      console.log(this.residente.progreso);
      this.residente.progreso[this.residente.progreso.length - 1].estado =
        "finalizado";
      if (this.progresoFase.fase === 2) {
        fecha = moment()
          .add(1, "year")
          .calendar();
      } else if (this.progresoFase.fase === 3) {
        fecha = moment()
          .add(6, "months")
          .calendar();
      }
      var date = fecha.split("/");
      return date[2].toString() + "-" + date[1] + "-" + date[0];
    },
    limpiar() {
      this.activado = false;
      this.switchPromocion = false;
      this.$refs.myVueDropzone.removeAllFiles();
      (this.progresoResidente = {
        fase: "",
        nombre: "",
        fechaingreso: moment().format("L"),
        estado: "",
      }),
        (this.progresoFase = {
          educativa: {
            documentos: [],
            estado: "incompleto",
          },
          social: {
            documentos: [],
            estado: "incompleto",
          },
          psicologica: {
            documentos: [],
            estado: "incompleto",
          },
          fase: "",
          documentotransicion: {
            fecha: moment().format("L"),
            idcreador: "",
            observaciones: "",
            firma: {
              urlfirma: "",
              nombre: "",
              cargo: "",
            },
          },
        });
    },
    async abrirDialogoDetalleFase(idresidente) {
      this.dialogofase = !this.dialogofase;
    },
    async obtenerFase(idresidente) {
      var fase = {};
      await axios
        .get("/fase/id?id=" + idresidente)
        .then((res) => {
          console.log(res);
          fase = res.data;
        })
        .catch((err) => console.log(err));
      console.log(fase);
      return fase;
    },generarFechaDocumentos(meses){
      
        var fecha = new Date();
            fecha.setMonth(fecha.getMonth()+meses);
            fecha.setHours(0);
            fecha.setMinutes(0);
            fecha.setSeconds(0);
            return fecha.toISOString();
    },
    async registrarDocumentoTransicionFase() {
      this.activado = true;
      if (this.switchPromocion === true) {
        this.mensaje(
          "error",
          "..Oops",
          "No se puede promover al residente",
          "<strong>Completar los documentos de su fase actual<strong>"
        );
      } else if (
        this.$v.progresoFase.$invalid ||
        this.switchPromocion === true
      ) {
        console.log("hay errores al modificar p llama");
        this.mensaje(
          "error",
          "..Oops",
          "Se encontraron errores en el formulario",
          "<strong>Verifique los campos Ingresados<strong>"
        );
      } else {
        console.log(this.progresoFase.documentotransicion.fecha);
        this.progresoFase.documentotransicion.fecha = this.convertDateFormat(
          this.progresoFase.documentotransicion.fecha
        );
        console.log(
          "primero fecha de documentotransicion: " +
            this.progresoFase.documentotransicion.fecha
        );
        this.progresoFase.documentotransicion.firma.nombre =
          this.user.datos.nombre + " " + this.user.datos.apellido;
        this.progresoFase.documentotransicion.firma.cargo = this.user.rol.nombre;
        this.progresoFase.documentotransicion.idcreador = this.user.id;
        if (this.progresoFase.fase === 2) {
          this.progresoResidente.fase = 2;
          this.progresoResidente.nombre = "desarrollo";
          this.progresoFase.educativa.documentos = [
            {
              tipo: "PlanIntervencionIndividualEducativo",
              estado: "Pendiente",
              fechaestimada: this.generarFechaDocumentos(0)
            },
            { tipo: "InformeEducativoEvolutivo", estado: "Pendiente" , fechaestimada: this.generarFechaDocumentos(1)},
          ];
          this.progresoFase.social.documentos = [
            {
              tipo: "PlanIntervencionIndividualSocial",
              estado: "Pendiente",
              fechaestimada: this.generarFechaDocumentos(0)
            },
            { tipo: "InformeSocialEvolutivo", estado: "Pendiente",
              fechaestimada: this.generarFechaDocumentos(1) },
          ];
          this.progresoFase.psicologica.documentos = [
            {
              tipo: "PlanIntervencionIndividualPsicologico",
              estado: "Pendiente",
              fechaestimada: this.generarFechaDocumentos(0)
            },
            { tipo: "InformePsicologicoEvolutivo", estado: "Pendiente" ,
            fechaestimada: this.generarFechaDocumentos(1)},
          ];
        } else if (this.progresoFase.fase === 3) {
          this.progresoResidente.fase = 3;
          this.progresoResidente.nombre = "reinserción";
          this.progresoFase.educativa.documentos = [
            { tipo: "InformeEducativoFinal", estado: "Pendiente",
            fechaestimada: this.generarFechaDocumentos(0) },
          ];
          this.progresoFase.social.documentos = [
            { tipo: "InformeSocialFinal", estado: "Pendiente",
            fechaestimada: this.generarFechaDocumentos(0) },
            { tipo: "ActaExternamiento", estado: "Pendiente", 
            fechaestimada: this.generarFechaDocumentos(1)},
          ];
          this.progresoFase.psicologica.documentos = [
            { tipo: "InformePsicologicoFinal", estado: "Pendiente",
              fechaestimada: this.generarFechaDocumentos(0) },
          ];
        }
        //this.progresoFase.documentotransicion.firma.urlfirma = "https://siscarfileserver2.blob.core.windows.net/firmas/19f70067-5c30-444f-a83b-2f456f6a27bd.jpg";
        this.progresoResidente.fechaingreso = this.convertDateFormat(this.progresoResidente.fechaingreso) + "T05:00:00Z";
        this.progresoResidente.fechafinalizacion = this.calculoFin() + "T05:00:00Z";
        this.progresoResidente.estado = "inicio";
        //Actualizando en Residente
        this.residente.progreso[
          this.residente.progreso.length - 1
        ].fechafinalizacion = this.progresoResidente.fechaingreso;
        let faseAnterior = this.residente.progreso[
          this.residente.progreso.length - 1
        ].fase;
        this.residente.progreso.push(this.progresoResidente);
        console.log(this.residente);
        console.log(this.progresoFase);
        var residenteFase = {
          residente: this.residente,
          progresoFase: this.progresoFase,
          promocion: true,
          faseAnterior,
        };
        await axios
          .put("/Residente", residenteFase)
          .then((res) => {
            console.log(res.data);
            var info = {
              apellido: res.data.apellido,
              estado: res.data.estado,
              fechaIngreso: res.data.fechaIngreso.split("T")[0],
              fechaNacimiento: res.data.fechaNacimiento.split("T")[0],
              id: res.data.id,
              juzgadoProcedencia: res.data.juzgadoProcedencia,
              lugarNacimiento: res.data.lugarNacimiento,
              motivoIngreso: res.data.motivoIngreso,
              nombre: res.data.nombre,
              numeroDocumento: res.data.numeroDocumento,
              progreso: res.data.progreso,
              sexo: res.data.sexo,
              telefonosReferencia: res.data.telefonosReferencia,
              tipoDocumento: res.data.tipoDocumento,
              ubigeo: res.data.ubigeo,
            };
            this.replaceResidente(info);
            this.progresoResidente = {};
            this.cerrarDialogo();
          })
          .catch((err) => {
            console.log(err);
            // await this.mensaje(
            // "error",
            // "Oops...",
            // "Hubo un problema al realizar la promoción, intentelo nuevamente",
            // "<strong>Se redirigira a la interfaz de Gestión<strong>"
            // );
          });
        await this.mensaje(
          "success",
          "Listo",
          "Residente promovido satisfactoriamente",
          "<strong>Se redirigira a la interfaz de Gestión<strong>"
        );

        this.$v.progresoFase.$reset();
      }
    },
  },
  computed: {
    ...mapGetters(["user"]),

    errorSeleccionFase() {
      const errors = [];
      if (!this.$v.progresoFase.fase.$dirty) return errors;
      !this.$v.progresoFase.fase.required &&
        errors.push(
          "Debe ingresar la fase a la que será promovido el residente"
        );
      return errors;
    },
    errorObservaciones() {
      const errors = [];
      if (!this.$v.progresoFase.documentotransicion.observaciones.$dirty)
        return errors;
      !this.$v.progresoFase.documentotransicion.observaciones.required &&
        errors.push("Debe ingresar una observación Obligatoriamente");
      !this.$v.progresoFase.documentotransicion.observaciones.minLength &&
        errors.push("Las observaciones deben tener al menos 3 caracteres");
      !this.$v.progresoFase.documentotransicion.observaciones.esParrafo &&
        errors.push(
          "Las observaciones no deben contener caracteres especiales"
        );
      return errors;
    },
    errorUrlFirma() {
      if (this.activado) {
        return this.$v.progresoFase.documentotransicion.firma.urlfirma
          .required == false &&
          this.$v.progresoFase.documentotransicion.firma.urlfirma.$dirty ==
            false
          ? true
          : false;
      }
    },
  },

  validations() {
    return {
      progresoFase: {
        fase: {
          required,
        },
        documentotransicion: {
          observaciones: {
            required,
            esParrafo,
            minLength,
          },
          firma: {
            //Falta validar esto y hacer los metodos de error
            urlfirma: {
              required,
            },
          },
        },
      },
    };
  },
};
</script>

<style scoped>
div.btn__content {
  padding: 0;
}
div.card__actions .btn {
  min-width: 0;
}
</style>

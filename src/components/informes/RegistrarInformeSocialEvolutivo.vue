<template>
  <v-dialog v-model="show" max-width="880px">
    <v-card>
      <v-card-title class="justify-center">{{ titulo }}</v-card-title>
      <v-stepper v-model="step">
        <v-stepper-header>
          <v-stepper-step editable step="1">
            Datos Generales
          </v-stepper-step>
          <v-divider></v-divider>
          <v-stepper-step editable step="2">
            Análisis y Diagnóstico
          </v-stepper-step>
        </v-stepper-header>
        <!-- fdsfs -->
        <v-stepper-items>
          <v-stepper-content step="1">
            <div class="container-user">
              <form>
                <v-autocomplete
                  v-model="informe.idresidente"
                  :items="listaresidentes"
                  filled
                  chips
                  dense
                  outlined
                  color="#009900"
                  label="Usuaria CAR"
                  item-text="nombre"
                  item-value="id"
                  @input="$v.informe.idresidente.$touch()"
                  @blur="$v.informe.idresidente.$touch()"
                  :error-messages="errorResidente"
                >
                  <template v-slot:selection="data">
                    <v-chip
                      v-bind="data.attrs"
                      :input-value="data.selected"
                      style="margin-top:5px"
                    >
                      <v-avatar left color="#b3b3ff" size="24">
                        <span style="font-size:12px">UE</span>
                      </v-avatar>
                      {{ data.item.nombre + " " + data.item.apellido }}
                    </v-chip>
                  </template>
                  <template v-slot:item="data">
                    <template>
                      <v-list-item-avatar>
                        <v-avatar left color="#b3b3ff" size="24">
                          <span style="font-size:12px">US</span>
                        </v-avatar>
                      </v-list-item-avatar>
                      <v-list-item-content>
                        <v-list-item-title
                          >Nombre completo: {{ data.item.nombre }}
                          {{ data.item.apellido }}
                        </v-list-item-title>
                        <v-list-item-subtitle
                          >Nro. Documento:
                          {{ data.item.numerodocumento }}</v-list-item-subtitle
                        >
                      </v-list-item-content>
                    </template>
                  </template>
                </v-autocomplete>
               <!-- <v-autocomplete
                  :items="listasociales"
                  filled
                  chips
                  dense
                  outlined
                  v-model="informe.contenido.evaluador"
                  color="#009900"
                  label="Evaluador"
                  item-text="usuario"
                  item-value="id"
                  :error-messages="errorEvaluador"
                >
                  <template v-slot:selection="data">
                    <v-chip
                      v-bind="data.attrs"
                      :input-value="data.selected"
                      style="margin-top:5px"
                    >
                      <v-avatar left color="#b3b3ff" size="24">
                        <span style="font-size:12px">RT</span>
                      </v-avatar>
                      {{ data.item.datos.nombre }}
                      {{ data.item.datos.apellido }}
                    </v-chip>
                  </template>
                  <template v-slot:item="data">
                    <template>
                      <v-list-item-avatar>
                        <v-avatar left color="#b3b3ff" size="24">
                          <span style="font-size:12px">UC</span>
                        </v-avatar>
                      </v-list-item-avatar>
                      <v-list-item-content>
                        <v-list-item-title
                          >Nombre completo: {{ data.item.datos.nombre }}
                          {{ data.item.datos.apellido }}
                        </v-list-item-title>
                        <v-list-item-subtitle
                          >Nro. Documento:
                          {{
                            data.item.datos.numerodocumento
                          }}</v-list-item-subtitle
                        >
                      </v-list-item-content>
                    </template>
                  </template>
                </v-autocomplete>-->
                <v-textarea
                  v-model="informe.contenido.antecedentes"
                  label="Antecedentes"
                  auto-grow
                  outlined
                  @input="$v.informe.contenido.antecedentes.$touch()"
                  @blur="$v.informe.contenido.antecedentes.$touch()"
                  :error-messages="errorAntecedentes"
                  rows="2"
                  row-height="40"
                  color="#009900"
                  shaped
                ></v-textarea>
                <v-textarea
                  v-model="informe.contenido.situacionactual"
                  label="Situación Social Actual"
                  auto-grow
                  outlined
                  @input="$v.informe.contenido.situacionactual.$touch()"
                  @blur="$v.informe.contenido.situacionactual.$touch()"
                  :error-messages="errorSituacionSocial"
                  rows="2"
                  row-height="40"
                  color="#009900"
                  shaped
                ></v-textarea>
                <v-textarea
                  v-model="informe.contenido.diagnosticosocial"
                  label="Diagnostico Social"
                  auto-grow
                  outlined
                  @input="$v.informe.contenido.diagnosticosocial.$touch()"
                  @blur="$v.informe.contenido.diagnosticosocial.$touch()"
                  :error-messages="errorDiagnosticoSocial"
                  rows="2"
                  row-height="40"
                  color="#009900"
                  shaped
                ></v-textarea>
                <v-row>
                  <v-col>
                    <v-btn block @click="step = 2" color="success">
                      <v-icon left>mdi-page-next-outline</v-icon>
                      <span>Continuar</span>
                    </v-btn>
                  </v-col>
                  <v-col>
                    <v-btn block @click="cerrarDialogo()" color="primary">
                      <v-icon left>mdi-close-outline</v-icon>
                      <span>Cerrar</span>
                    </v-btn>
                  </v-col>
                </v-row>
              </form>
            </div>
          </v-stepper-content>
          <v-stepper-content step="2">
            <div class="container-user">
              <form>
                <v-card
                  style="margin-top:1%;margin-bottom:1%;padding-bottom:1%;background-color:#EAEAEA"
                >
                  <v-card
                    elevation="0"
                    style="background-color:#EAEAEA"
                    height="70"
                  >
                    <v-row style="margin:1%;heigh:100%" align="center">
                      <v-col :cols="8" align="left">
                        <v-text-field
                          v-model="recomendacion"
                          label="Recomendaciones"
                          color="#009900"
                          @input="$v.recomendacion.$touch()"
                          @blur="$v.recomendacion.$touch()"
                          :error-messages="errorRecomendacion"
                        ></v-text-field>
                      </v-col>
                      <v-col :cols="4" align="right">
                        <v-btn
                          fab
                          small
                          dark
                          color="green"
                          @click="agregarRecomendaciones"
                        >
                          <v-icon dark>
                            mdi-plus
                          </v-icon>
                        </v-btn>
                      </v-col>
                    </v-row>
                  </v-card>

                  <v-card
                    tile
                    elevation="0"
                    color="#FAFAFA"
                    style="margin:5px"
                    height="60"
                    v-for="recomendacion in recomendaciones"
                    :key="recomendacion"
                  >
                    <v-row style="margin-left:10px;heigh:100%" align="center">
                      <v-col :cols="8" align="left">
                        <span>{{ recomendacion }}</span>
                      </v-col>
                      <v-col :cols="4" align="right">
                        <div style="margin-right:20px">
                          <v-btn
                            fab
                            x-small
                            dark
                            color="red"
                            @click="eliminarRecomendaciones(recomendacion)"
                          >
                            <v-icon dark>
                              mdi-minus
                            </v-icon>
                          </v-btn>
                        </div>
                      </v-col>
                    </v-row>
                  </v-card>
                </v-card>

                <v-card
                  style="margin-top:30px;padding:5px 5px;background-color:#EAEAEA"
                >
                      <div>
                        <vue-dropzone
                        ref="myVueDropzone2"
                        @vdropzone-success="afterSuccess"
                        @vdropzone-removed-file="afterRemoved"
                        id="dropzone"
                        :options="dropzoneOptions"
                        >
                        </vue-dropzone>
                      </div>
                </v-card>

                <v-card
                  style="margin-top:30px;padding:5px 5px;background-color:#EAEAEA"
                >
                  <v-card
                    elevation="0"
                    style="background-color:#EAEAEA"
                    height="70"
                  >
                    <v-row style="margin:1%;heigh:100%" align="center">
                      <v-col :cols="4" align="left">
                        <v-text-field
                          v-model="this.user.usuario"
                          label="Nombre"
                          color="#009900"
                          readonly
                        ></v-text-field>
                      </v-col>
                      <v-col :cols="4" align="left">
                        <v-text-field
                          v-model="this.user.rol.nombre"
                          label="Cargo"
                          color="#009900"
                          readonly
                        ></v-text-field>
                      </v-col>
                    </v-row>
                  </v-card>
                  <v-row>
                    <v-col :cols="12" align="center">
                      <div>
                        <v-card-text>
                              <img
                                width="240"
                                height="170"
                                :src="this.user.datos.firma"
                                alt=""
                              />
                        </v-card-text>
                      </div>
                    </v-col>
                  </v-row>
                </v-card>
               
                <v-row>
                  <v-col>
                    <v-btn block @click="registrarInforme" color="success">
                      <v-icon left>mdi-page-next-outline</v-icon>
                      <span>Registrar Informe</span>
                    </v-btn>
                  </v-col>
                  <v-col>
                    <v-btn block @click="show = false" color="primary">
                      <v-icon left>mdi-close-outline</v-icon>
                      <span>Cerrar</span>
                    </v-btn>
                  </v-col>
                </v-row>
              </form>
            </div>
          </v-stepper-content>
        </v-stepper-items>
      </v-stepper>
    </v-card>
  </v-dialog>
</template>
<script>
import axios from "axios";
import vue2Dropzone from "vue2-dropzone";
import "vue2-dropzone/dist/vue2Dropzone.min.css";
import { mapMutations, mapState } from "vuex";
import { required, minLength, email, helpers } from "vuelidate/lib/validators";
import moment from "moment";
import { mapGetters } from "vuex";
function esParrafo(value) {
  return /^[A-Za-z\d\s.,;°"“()áéíóúÁÉÍÓÚñÑ]+$/.test(value); 
}
function esTexto(value) {
  return /^[A-Za-z\sáéíóúÁÉÍÓÚñÑ]+$/.test(value); 
}
export default {
  props: ["listaresidentes", "visible", "titulo"],
  components: {
    vueDropzone: vue2Dropzone,
  },
  data() {
    return {
      fileList: [],
      datemenu: false,
      step: 1,
      dropzoneOptions: {
        url: "https://httpbin.org/post",
        thumbnailWidth: 250,
        maxFilesize: 10.0,
        maxFiles: 10,
        acceptedFiles: ".pdf",
        headers: { "My-Awesome-Header": "header value" },
        addRemoveLinks: true,
        dictDefaultMessage:
          "Seleccione un archivo anexo de su dispositivo o arrástrela aquí",
      },
      recomendacion: "",
      recomendaciones: [],
      informe: {
        id: "",
        tipo: "",
        historialcontenido: [],
        creadordocumento: "5f9e4cdae4655cf92eaa4d5b",  
        fechacreacion: null,
        area: "social",
        fase: "2",
        idresidente: "",
        estado: "creado",
        contenido: {
          antecedentes: "",
          situacionactual: "",
          diagnosticosocial: "",
          recomendaciones: [],
          anexos: [],
          codigodocumento: "",
          //evaluador: "",
        },
      },
    };
  },
  async created() {
    this.recomendaciones = "";
    this.recomendacion = "";
  },
  methods: {
    ...mapMutations(["addInforme"]),
    async sendPDFFiles() {
      let listaTitulos = [];
      let listaanexos = this.fileList;
      for (let index = 0; index < this.fileList.length; index++) {
        let formData = new FormData();
        listaTitulos.push(this.fileList[index].name)
        formData.append("file", this.fileList[index]);
        await axios
          .post("/Media/archivos/pdf", formData)
          .then((res) => {
            listaanexos[index] = res.data;
          })
          .catch((err) => console.log(err));
      }
      for (let index = 0; index < this.fileList.length; index++) {
        this.informe.contenido.anexos.push(
          {
            url: listaanexos[index],
            titulo: listaTitulos[index],
          }
        )
      }
      console.log(this.informe.contenido.anexos);
    },
    async obtenerFaseResidente(idresidente){
      var info = {};
      await axios
        .get("/residente/id?id=" + idresidente)
        .then((res) => {
          info = res.data;
        })
        .catch((err) => console.log(err));
      console.log(info.progreso[info.progreso.length - 1].fase);
      return info.progreso[info.progreso.length - 1].fase.toString();
    },
    async registrarInforme() {
      this.informe.creadordocumento = this.user.id;
      await this.sendPDFFiles();
      if (this.titulo === "Registrar Informe Social Evolutivo") {
        this.informe.fase = await this.obtenerFaseResidente(this.informe.idresidente);
        this.informe.tipo = "InformeSocialEvolutivo";
      } else {
        this.informe.fase = "3"
        this.informe.tipo = "InformeSocialFinal";
      }
      console.log(this.informe);
      this.$v.informe.$touch();
      if (this.$v.informe.$invalid) {
        console.log("hay errores");
        this.mensaje(
          "error",
          "..Oops",
          "Se encontraron errores en el formulario",
          "<strong>Verifique los campos Ingresados<strong>"
        );
      } else {
        console.log("no hay errores");
        console.log(this.informe);
        await axios
          .post("/informe/informese", this.informe)
          .then((res) => {
            this.informe = res.data;
            console.log(this.listaresidentes);
            var resi = this.listaresidentes.filter(function(residente) {
              return residente.id == res.data.idresidente;
            });
            console.log(resi);
            var info = {
              id: res.data.id,
              tipo: res.data.tipo.replace(/([a-z])([A-Z])/g, "$1 $2"),              
              codigodocumento: res.data.contenido.codigodocumento,
              nombrecompleto: resi[0].nombre + " " + resi[0].apellido,
              fechacreacion : res.data.fechacreacion.split("T")[0],
            };
            console.log("HOLA");
            console.log(info);
            this.addInforme(info);
            this.cerrarDialogo();
          })
          .catch((err) => console.log(err));
        await this.mensaje(
          "success",
          "Listo",
          "Informe registrado Satisfactoriamente",
          "<strong>Se redirigira a la interfaz de gestión<strong>"
        );
      }
    },
    agregarRecomendaciones() {
      this.$v.recomendacion.$touch();
      if (!this.$v.recomendacion.$invalid) {
        let recomendaciones = this.recomendacion;
        this.informe.contenido.recomendaciones.push(recomendaciones);
        this.recomendaciones = this.informe.contenido.recomendaciones;
        this.recomendacion = "";
        this.$v.recomendacion.$reset();
      }
    },
    eliminarRecomendaciones(recomendacion) {
      this.recomendaciones.forEach(function(car, index, object) {
        if (car === recomendacion) {
          object.splice(index, 1);
        }
      });
    },
    cerrarDialogo() {
      this.informe = this.limpiarInforme();
      this.step = 1;
      this.$emit("close");
    },
    async mensaje(icono, titulo, texto, footer) {
      await this.$swal({
        icon: icono,
        title: titulo,
        text: texto,
        footer: footer,
      });
    },
    afterSuccess(file, response) {
      this.fileList.push(file);
      console.log(this.fileList.length)
    },
    afterRemoved(file, error, xhr) {
      this.fileList.forEach(function(car, index, object) {
        if (car === file) {
          object.splice(index, 1);
        }
      });
      console.log(this.fileList.length)
    },
    limpiarInforme() {
      return {
        tipo: "",
        historialcontenido: [],
        creadordocumento: "",
        fechacreacion: null,
        area: "social",
        fase: "acogida",
        idresidente: "",
        estado: "creado",
        contenido: {
          antecedentes: "",
          situacionactual: "",
          diagnosticosocial: "",
          recomendaciones: [],
          anexos: [],
          codigodocumento: "",
        },
      };
    },
  },
  computed: {
    ...mapGetters(["user"]),
    verifyColor() {
      return "red";
    },
    errorAntecedentes() {
      const errors = [];
      if (!this.$v.informe.contenido.antecedentes.$dirty) return errors;
      !this.$v.informe.contenido.antecedentes.required &&
        errors.push("Debe ingresar un antecedente");
      !this.$v.informe.contenido.antecedentes.esParrafo &&
        errors.push("El antecedente no debe contener caracteres especiales");
      return errors;
    },
    errorSituacionSocial() {
      const errors = [];
      if (!this.$v.informe.contenido.situacionactual.$dirty) return errors;
      !this.$v.informe.contenido.situacionactual.required &&
        errors.push("Debe ingresar la situación actual");
      !this.$v.informe.contenido.situacionactual.esParrafo &&
        errors.push("La situacion actual no debe contener caracteres especiales");
      return errors;
    },
    errorResidente() {
      const errors = [];
      if (!this.$v.informe.idresidente.$dirty) return errors;
      !this.$v.informe.idresidente.required &&
        errors.push("Debe seleccionar un residente obligatoriamente");
      return errors;
    },
    errorDiagnosticoSocial(){
      const errors = [];
      if (!this.$v.informe.contenido.diagnosticosocial.$dirty) return errors;
      !this.$v.informe.contenido.diagnosticosocial.required &&
        errors.push("Debe ingresar un diagnostico");
      !this.$v.informe.contenido.diagnosticosocial.esParrafo &&
        errors.push("El diagnostico social no debe contener caracteres especiales");
      return errors;
    },/*
    errorEvaluador() {
      const errors = [];
      if (!this.$v.informe.contenido.evaluador.$dirty) return errors;
      !this.$v.informe.contenido.evaluador.required &&
        errors.push("Debe seleccionar un evaluador obligatoriamente");
      return errors;
    },*/    
    errorRecomendacion() {
      const errors = [];
      if (!this.$v.recomendacion.$dirty) return errors;
      !this.$v.recomendacion.required &&
        errors.push("Debe registrar la recomendacion obligatoriamente");
      !this.$v.recomendacion.esParrafo &&
        errors.push(
          "La recomendacion no debe contener caracteres especiales"
        );
      return errors;
    },
    show: {
      get() {
        return this.visible;
      },
      set(value) {
        if (!value) {
          this.$emit("close");
        }
      },
    },
  },
  validations() {
    return {
      informe: {
        idresidente: {
          required,
        },        
        contenido: {
          antecedentes: {
            required,
            esParrafo,
          },
          situacionactual: {
            required,
            esParrafo,
          },
          diagnosticosocial: {
            required,
            esParrafo,
          },
          /*evaluador:{
            required,
          },*/
        },
      },
       recomendacion: {
        required,
        esParrafo,
      },
    };
  },
};
</script>

<style scoped>
.container-user {
  margin: 15px;
}

.dropzone-custom-content {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  text-align: center;
}

.dropzone-custom-title {
  margin-top: 0;
  color: #00b782;
}

.subtitle {
  color: #314b5f;
}
.divider-custom {
  margin-top: 7px;
  margin-bottom: 7px;
}

.inputTextField {
  border-color: green;
}
</style>

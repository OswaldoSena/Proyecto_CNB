<template>
    <div class="mt-0">
      <h5 style="color:#08088A">{{ msg }}</h5>
      <b-container>     
        <!--*******************FORMULARIO INGREAR EVENTO*************-->
        <form @submit.prevent="Agregarcurso">
          <b-row class="m-3">         
            <b-col>             
              <b-form-input 
                type="text" 
                v-model="NuevoCurso"  
                placeholder="Aquí puede ingresar nuevo evento" 
                >
              </b-form-input>
            </b-col>
            <b-col  class="d-flex justify-content-start">
              <b-button
                size="sm" 
                variant="outline-success" 
                type="submit" 
                >
                Ingresar Evento
              </b-button>
            </b-col> 
          </b-row>
        </form>
        <div class="box"> <!--***************TABLA CONTENIDO EVENTO--> 
          <vue-scrolling-table bordered
            :scroll-horizontal="scrollHorizontal"
            :scroll-vertical="scrollVertical"
            :sync-header-scroll="syncHeaderScroll"
            :sync-footer-scroll="syncFooterScroll"
            :include-footer="includeFooter"
            :dead-area-color="deadAreaColor"
            :class="{ freezeFirstColumn:freezeFirstColumn }"
            >
            <!-- head -->
            <template slot="thead">
              <tr>
                <th 
                  v-for="col in columns" 
                  :class="col.cssClasses"
                  :key="col.id"
                  >
                  {{ col.title }}
                </th>
                <th 
                  class="action"
                  >
                  Acciones
                </th> 
              </tr>
            </template>
            <!-- body -->
            <template slot="tbody">
                <tr  
                  v-for="item in cursovirtual" 
                  :key='item.value'
                  >
                <td 
                  v-for="col in columns"
                  :class="col.cssClasses"
                  :key="col.id">{{ item[col.id] }}  
                </td>
                <td>
                  <b-button 
                    class="m-1"
                    size="sm" 
                    variant="outline-success"                  
                    v-b-modal.modalEdicion 
                    @click="EditarCurso(
                      item.value,
                      item.text,
                      item.ESTADO,
                      item.NumeroHoras,
                      item.TipoDiploma
                      )"
                    >
                    Editar
                  </b-button>          
                  <b-button 
                    class="m-1"
                    size="sm" 
                    variant="outline-danger" 
                    @click="EliminarCurso(item.value)"
                    >
                    Eliminar
                  </b-button>
                </td>
              </tr>          
            </template>
          </vue-scrolling-table>
        </div>
        <!--********************FORMULARIO B-MODAL EDICION CURSOS***************** -->
        <div id="modal">
          <b-form ref="form" @submit.stop.prevent="handleSubmit">
            <b-modal
              id="modalEdicion"
              size="xl"
              ref="modal"
              title="Formulario de Edición"
              valor= item.Id_Curso
              @show="resetModal"
              @hidden="resetModal"
              @ok="handleOk"
              >
              <main class="row">             
                <div class="row g-2">
                  <!---Input Descripción del evento-->
                  <b-form-group class="col-sm m-2"
                    :state="nameState"
                    label="Descripción del Evento"
                    label-for="name-input"
                    invalid-feedback="Name is required"
                    >
                    <b-form-input
                      id="name-input"
                      v-model="nameEditar"
                      :state="nameState"
                      required
                      >
                    </b-form-input>
                  </b-form-group>
                  <!-- seleccion Estado curso -->
                  <b-form-group class="col-sm m-2"
                    id="inputEstadoCurso"
                    :state="SeleccionCursoEstado"
                    label="Estado Curso:"
                    label-for="estado"
                    invalid-feedback="Debe seleccionar el estado del curso"
                    > 
                    <b-form-select
                      class="form-select"
                      :state="SeleccionCursoEstado" 
                      v-model="SelectEstado" 
                      id="EstadonCurso"
                      label="Seleccione Estado:" 
                      descripcion="estado"
                      :options="options" 
                      required
                      >
                    </b-form-select>
                  </b-form-group>
                </div>         
                  <!--Selección Diploma -->
                <div class="row g-2">
                  <b-form-group class="col-sm m-2"   
                    id="inputSelect"          
                    label="Diploma Curso:"
                    label-for="diploma"
                    invalid-feedback="Debe seleccionar diploma del curso"
                    >
                    <b-form-select
                      class="form-select"         
                      id="diploma"
                      v-model="SelectDiploma" 
                      descripcion="diploma"
                      :options="optionsDiploma" 
                      required
                      >              
                    </b-form-select>
                  </b-form-group>        
                  <!-- input elegir archivo Imagen --> 
                  <div class="form-group col-auto m-2">
                    <label for="upFileLoad">Cargue una Imagen:</label>
                  <input 
                    class="form-control" 
                    type="file" 
                    id="newImagen" 
                    name="upFileLoad" 
                    accept="image/*"
                    @change="cargaImagen"
                    >
                  </div>
                </div>
                <!--Selección Horas del Curso-->
                <div class="row g-2">
                  <b-form-group class="col-sm m-2"
                    :state="nameHora"
                    label="Horas del Curso"
                    label-for="name-input"
                    invalid-feedback="Name is required"
                  >
                    <b-form-input
                      id="name-input"
                      v-model="nameEditarHoras"
                      :state="nameHora"
                      required
                    ></b-form-input>
                  </b-form-group> 
                <!-- Selección de texto a configurar -->
                <b-form-group class="col-sm m-2"
                  id="confiText"
                  label ="Seleccione Texto a Configurar:"
                  label-for="inputConfiText"
                  >
                  <b-form-select 
                  class="form-select"
                  id="inputConfiText" 
                  v-model="selectedText"
                  :options="opcionConfiTexto"
                  >
                  </b-form-select>
                </b-form-group>
                </div>
                <!-- ********** VISTA PREVIA**********-->
                <div class="col mt-2">
                  <div class="card text-sm-center mb-5 text-wrap">
                    <div v-if ="imagenSelecciona">
                    <img 
                      class="card-img" 
                      :src="imagenSelecciona" 
                      alt="certificado"
                      >              
                  </div>
                  <!-- Superposición de la imagen con textos configurables -->
                    <div class="card-img-overlay">              
                      <!-- Parrafo nombre curso-->
                      <p
                        class="card-text"
                        :style="{
                          color: formData.ColorNombre,
                          fontSize: formData.TamañoNombre + 'px',
                          position: 'relative',
                          top: formData.PosicionNombre + 'px'
                        }"
                      >
                        {{ formData.NombreCurso }}                
                      </p>
                      <!-- Parrafo cédula -->
                      <p 
                        class="card-text"
                        :style="{
                          fontSize: formData.TamañoCedula + 'px',
                          color: formData.ColorCedula,
                          position: 'relative',
                          top: formData.PosicionCedula + 'px'
                        }"
                        >
                        {{ formData.Cedula }}
                      </p>
                        <!-- Parrafo Texto1 -->
                        <p 
                        class="card-text"
                        :style="{
                          fontSize: formData.Tamaño1 + 'px',
                          color: formData.Color1,
                          position: 'relative',
                          top: formData.Posicion1 + 'px'
                        }"
                        >
                        <small>{{ formData.Texto1 }}</small>
                      </p>
                      <!-- Parrafo Texto2 -->
                      <p 
                        class="card-text"
                        :style="{
                          fontSize: formData.Tamaño2 + 'px',
                          color: formData.Color2,
                          position: 'relative',
                          top: formData.Posicion2 + 'px'
                        }"
                        >
                        <small>{{ formData.Texto2 }}</small>
                      </p>
                      <!-- Parrafo Texto3 -->
                      <p 
                        class="card-text"
                        :style="{
                          fontSize: formData.Tamaño3 + 'px',
                          color: formData.Color3,
                          position: 'relative',
                          top: formData.Posicion3 + 'px'
                        }"
                        >
                        <small>{{ formData.Texto3 }}</small>
                      </p>
                        <!-- Parrafo Fecha -->
                        <p 
                        class="card-text"
                        :style="{
                          fontSize: formData.TamañoFecha + 'px',
                          color: formData.ColorFecha,
                          position: 'relative',
                          top: formData.PosicionFechaY + 'px',
                          left: formData.PosicionFechaX + 'px'
                        }"
                        >
                        <small>{{ formData.Fecha }}</small>
                      </p>              
                    </div>
                  </div>
                </div>
                <!-- *********************** FORMULARIO CONFIGURACION TEXTO DIPLOMA*******************************-->
                <div class="col">
                <form ref="form" @submit.stop.prevent="handleSubmit">
                  <!-- Formulario Inputs Nombre -->
                  <div v-if="selectedText === 'nombre'">
                    <div class="form-group">
                      <label class="form-label" for="NombreCurso">Nombre Curso:</label>
                      <input
                        required
                        class="form-control"
                        type="text"
                        placeholder="Escriba Nombre Curso..."
                        v-model="formData.NombreCurso"
                      >
                    </div>
                    
                    <div class="form-group">
                      <label for="color" class="form-label">Color:</label>
                      <input
                        class="form-control form-control-color m-3"
                        type="color"
                        id="color"
                        placeholder="Configurar Texto"
                        v-model="formData.ColorNombre"
                      >
                    </div>
                    <div class="form-group">
                      <label class="form-label" for="fontSize">Tamaño de letra:</label>
                      <input
                        class="form-range"
                        type="range"
                        min="20"
                        max="60"
                        id="fontSize"
                        v-model="formData.TamañoNombre"
                      >
                    </div>            
                    <div class="form-group">
                      <label for="position">Posición:</label>
                      <input
                        class="form-range"
                        type="range"
                        id="position"
                        v-model="formData.PosicionNombre"
                      >
                    </div>
                  </div>
  
                  <!-- Formulario Inputs Identificación -->
                  <div v-if="selectedText === 'identi'">
                    <div class="form-group">
                      <label class="form-label" for="cedula">Cédula:</label>
                      <input
                        class="form-control"
                        type="number"
                        placeholder="Escriba Identificación..."
                        v-model="formData.Cedula"
                      >
                    </div>
                    <div class="form-group">
                      <label for="colorId" class="form-label">Color:</label>
                      <input
                        class="form-control form-control-color m-3"
                        type="color"
                        id="colorIdenti"
                        placeholder="Configurar Texto"
                        v-model="formData.ColorCedula"
                        >
                      </div>
                    <div class="form-group">
                      <label class="form-label" for="fontSizeId">Tamaño de letra:</label>
                      <input
                        class="form-range"
                        type="range"
                        min="20"
                        max="60"
                        id="fontSizeIdenti"
                        v-model="formData.TamañoCedula"
                        >
                      </div>
                    <div class="form-group">
                      <label for="positionIdenti">Posición:</label>
                      <input
                        class="form-range"
                        type="range"
                        id="positionIdenti"
                        v-model="formData.PosicionCedula"
                        >
                      </div>
                    </div>
  
                  <!-- Formulario Input Fecha -->
                  <div v-if="selectedText === 'fecha'">
                    <div class="form-group">
                      <label for="date">Fecha:</label>
                      <input
                        class="form-control"
                        type="date"
                        id="date"
                        v-model="formData.Fecha"
                      >
                    </div>
                    <div class="form-group">
                      <label for="colorFecha" class="form-label">Color:</label>
                      <input
                        class="form-control form-control-color m-3"
                        type="color"
                        id="colorFecha"
                        placeholder="Configurar Texto"
                        v-model="formData.ColorFecha"
                      >
                    <div class="form-group">
                      <label class="form-label" for="fontSizeFecha">Tamaño de letra:</label>
                      <input
                        class="form-range"
                        type="range"
                        min="15"
                        max="60"
                        id="fontSizeFecha"
                        v-model="formData.TamañoFecha"
                      >
                    </div>            
                    </div>
                    <div class="form-group">
                      <label for="positionFecha">Posición Vertical:</label>
                      <input
                        class="form-range"
                        type="range"
                        id="positionFecha"
                        v-model="formData.PosicionFechaY"
                      >
                    </div>
                    <div class="form-group">
                      <label for="positionFecha">Posición Horizontal:</label>
                      <input
                        class="form-range"
                        type="range"
                        id="positionFecha"
                        v-model="formData.PosicionFechaX"
                      >
                    </div>
                  </div>
                  <!-- Formulario Input Texto1 -->
                  <div v-if="selectedText === 'texto1'">
                      <div class="form-group">
                        <label for="Texto1">Texto 1:</label>
                        <input
                          class="form-control"
                          type="text"
                          id="Texto1"
                          v-model="formData.Texto1"
                        >
                      </div>
                      <div class="form-group">
                        <label for="colorTexto1" class="form-label">Color:</label>
                        <input
                          class="form-control form-control-color m-3"
                          type="color"
                          id="colorTexto1"
                          placeholder="Configurar Texto"
                          v-model="formData.Color1"
                        >
                      </div>
                      <div class="form-group">
                        <label class="form-label" for="fontSizeTexto1">Tamaño de letra:</label>
                        <input
                          class="form-range"
                          type="range"
                          min="20"
                          max="60"
                          id="fontSizeTexto1"
                          v-model="formData.Tamaño1"
                        >
                      </div>
                      <div class="form-group">
                        <label for="positionTexto1">Posición:</label>
                        <input
                          class="form-range"
                          type="range"
                          id="positionTexto1"
                          v-model="formData.Posicion1"
                        >
                      </div>
                    </div>
                  <!-- Formulario Input Texto2 --> 
                  <div v-if="selectedText === 'texto2'">
                      <div class="form-group">
                        <label for="Texto1">Texto 2:</label>
                        <input
                          class="form-control"
                          type="text"
                          id="Texto2"
                          v-model="formData.Texto2"
                        >
                      </div>
                      <div class="form-group">
                        <label for="colorTexto2" class="form-label">Color:</label>
                        <input
                          class="form-control form-control-color m-3"
                          type="color"
                          id="colorTexto2"
                          placeholder="Configurar Texto"
                          v-model="formData.Color2"
                        >
                      </div>
                      <div class="form-group">
                        <label class="form-label" for="fontSizeTexto1">Tamaño de letra:</label>
                        <input
                          class="form-range"
                          type="range"
                          min="20"
                          max="60"
                          id="fontSizeTexto1"
                          v-model="formData.Tamaño2"
                        >
                      </div>
                      <div class="form-group">
                        <label for="positionTexto2">Posición:</label>
                        <input
                          class="form-range"
                          type="range"
                          id="positionTexto2"
                          v-model="formData.Posicion2"
                        >
                      </div>
                  </div>
                          <!-- Formulario Input Texto3 -->
                          <div v-if="selectedText === 'texto3'">
                      <div class="form-group">
                        <label for="Texto3">Texto 3:</label>
                        <input
                          class="form-control"
                          type="text"
                          id="Texto1"
                          v-model="formData.Texto3"
                        >
                      </div>
                      <div class="form-group">
                        <label for="colorTexto3" class="form-label">Color:</label>
                        <input
                          class="form-control form-control-color m-3"
                          type="color"
                          id="colorTexto3"
                          placeholder="Configurar Texto"
                          v-model="formData.Color3"
                        >
                      </div>
                      <div class="form-group">
                        <label class="form-label" for="fontSizeTexto3">Tamaño de letra:</label>
                        <input
                          class="form-range"
                          type="range"
                          min="20"
                          max="60"
                          id="fontSizeTexto3"
                          v-model="formData.Tamaño3"
                        >
                      </div>
                      <div class="form-group">
                        <label for="positionTexto3">Posición:</label>
                        <input
                          class="form-range"
                          type="range"
                          id="positionTexto3"
                          v-model="formData.Posicion3"
                        >
                      </div>
                    </div>          
                  </form>      
                </div>
              </main>            
            </b-modal>
          </b-form>
        </div>     
      </b-container>
    </div>
  </template>
  <!--********************************************************************SCRIPT********************************************************************************-->
  <script>
  import {mapState} from 'vuex'  
  import axios from 'axios'
  import { mdbScrollbar,mdbTbl, mdbTblHead, mdbTblBody } from 'mdbvue';
  import VueScrollingTable from "vue-scrolling-table"
  //export const RUTA_SERVIDOR = process.env.VUE_APP_RUTA_API;
  
  
  //const $ = require('jquery')
  
  export default {
    name: 'cursovirtual',
    components: {
        mdbTbl,
        mdbTblHead,
        mdbTblBody,
        mdbScrollbar,
        VueScrollingTable
      },
    data(){
      return {
        //caracteristicas del scroll
        scrollVertical: true,
              scrollHorizontal: true,
              syncHeaderScroll: true,
              syncFooterScroll: true,
              includeFooter: true,
              deadAreaColor: "white",
              maxRows: 100,
        freezeFirstColumn: false,
        //v-model formulario
        formData: {        
          NombreCurso: '',
          TamañoNombre:'20',
          ColorNombre:'#000000',
          PosicionNombre:'0',
          //********************** */
          Cedula:'',
          TamañoCedula:'20',
          ColorCedula:'#000000',
          PosicionCedula:'0',
          /*********************** */
          Fecha:'',
          TamañoFecha:'20',
          ColorFecha:'#000000',
          PosicionFechaY:'0',
          PosicionFechaX:'0',
          /*********************** */ 
          Texto1: '',
          Tamaño1: '20',
          Color1: '#000000',
          Posicion1: '0',
          /*********************** */
          Texto2: '',
          Tamaño2: '20',
          Color2: '#000000',
          Posicion2: '0',
          /*********************** */
          Texto3: '',
          Tamaño3: '20',
          Color3: '#000000',
          Posicion3: '0',
          /*********************** */
          FondoDiploma:[],       
        },
        id:'',
        cursovirtual: null,
        NuevoCurso:'',
        errors: [],
        name: null,
        nameState: null,
        submittedNames: [],
        IdEdicion: '',
        nameEditar:'',
        nameEditarHoras:'',
        valid:false,
        SeleccionCursoEstado: null,
        SeleccionDiploma:null,
        SelectEstado: null,
        SelectDiploma: null,
        imgSelect:null,
        selectedText: null,
        nameHora:null,
  
        columns: [
          { id: "value", title: "ID", cssClasses: "w3" },
          { id: "text", title: "Descripción del Evento", cssClasses: "w4" },
          { id: "ESTADO", title: "Estado", cssClasses: "NroId" },
          { id: "NumeroHoras", title: "No. Horas", cssClasses: "NroId" },
          { id: "TipoDiploma", title: "Tipo Diploma", cssClasses: "NroId" },
          { id: "nombre",title: "Nombre Estudiante", cssClasses: "w4"},
          { id: "identi",title: "No. Identificación", cssClasses: "NroId"},
          { id: "texto1",title: "Texto 1", cssClasses: "w4"},
          { id: "texto2",title: "Texto 2", cssClasses: "w4"},
          { id: "texto3",title: "Texto 3", cssClasses: "w4"},
          { id: "fecha",title: "Fecha", cssClasses: "NroId"}
        ],
        options: [
          { value: null, text: 'Por favor seleccione una opción', disabled: true },
          { value: '1', text: 'ACTIVO' },
          { value: '0', text: 'INACTIVO' }
        ],  
        optionsDiploma: [
          { value: null, text: 'Por favor seleccione un diploma', disabled: true },
          { value: 'img1', text: 'NORMAL'},
          { value: 'img2', text: 'HOMIL'},
          { value: 'img3', text: 'TOMA MUESTRAS DE CITOLOGIA'},
          { value: 'img4', text: 'CONGRESO CLAHT'}
        ],
        opcionConfiTexto:[
          {value: null, text: "Seleccione Texto",disabled: true},
          {value:"nombre",text: "Nombre"},
          {value:"identi",text: "Identificación"},
          {value:"texto1",text: "Texto1"},
          {value:"texto2",text: "Texto2"},
          {value:"texto3",text: "Texto3"},
          {value:"fecha",text:"Fecha"},
         ],
      }
    },  
    /***************Se ejecuta cuando el componnete se monte *************/
    mounted(){
      this.getCursos();
       // Convertir imágenes al montar el componente
       this.convertirImagenesPorDefecto();
    },
    methods:{
      resetModal() {
          this.nameState = null;
          this.SeleccionCursoEstado = null;
          this.nameHora = null;
          this.SeleccionDiploma = null;
          this.name = '';
       },     
  
      /*************Methodo que llama la API para llenar array de cursos virtuales del CNB */
      getCursos(){
        axios.get("http://localhost:3000/cursos")
          .then (response =>{
            this.cursovirtual = response.data 
            console.log("cursos",response.data)       
        })
        .catch (e => console.log("Error al cargar cursos",e));
  
      },
      /*************Methodo que llamar a la API utilizado para eliminar el curso seleccionado */
      AñadirCursos(NuevoCurso){
        axios.post("http://localhost:3000/cursos",{NuevoCurso})
          .then (response =>{         
            if(response.data.NuevoCurso){
              alert("Registro insertado con éxito");
              this.getCursos(); 
            }else{
              alert("Error al tratar de insertar el registro, al parecer ya existe");
            }
          })
        .catch (e => console.log("Error al añdir curso", e));      
      },   
      Agregarcurso(){  
        if(this.NuevoCurso.length > 3 ){
          this.AñadirCursos(this.NuevoCurso);
          this.getCursos();                    
          this.NuevoCurso='';                        
        }else {
        alert("La descripción del curso debe tener mas de tres letras" )
        }
      },
      EditarCurso (index,text,estado,Horas,diploma,nomb,cedu,tex1,tex2,tex3,dia){
        this.resetModal();//se limpia el formulario modal
        this.IdEdicion = index
        this.nameEditar = text
        this.nameEditarHoras = Horas
        this.SeleccionDiploma = diploma
        this.formData.NombreCurso  = nomb
        this.formData.Cedula = cedu
        this.formData.Texto1 = tex1
        this.formData.Texto2 = tex2
        this.formData.Texto3 = tex3
        this.formData.Fecha = dia
        this.SelectEstado = estado==="ACTIVO" ? 1 : 0
        if (!this.IdEdicion) {
          alert('Error: ID de edición no definido');
          return;
        }      
      },
      EliminarCurso(idDelete){
        const strMensaje = 'Está seguro de eliminar el Registro?'
        if (confirm(strMensaje)){
          axios.delete(`http://localhost:3000/cursos/${idDelete}`)
          .then (response =>{
            if(response.data.ok){
            alert("Registro eliminado con éxito");
            this.getCursos(); 
          }else{
            alert("Error al tratar de eliminar el registro");
          } 
        })
        .catch (e =>{
          alert("Error de conexión. Intenta nuevamente.");
          console.log(e)
        });
        }else{
          return false;
        }
      },
      checkFormValidity() {
        const valid = this.$refs.form.checkValidity(); //verifica si el formulario es válido
        this.valid = valid; //Actualiza el estado de la validez
        return valid;
      },        
      handleOk(bvModalEvt) {
        // Previene el cierre automático del modal en Bootstrap-Vue
        bvModalEvt.preventDefault();
  
        //verifica si "IdEdicion" esta definido antes de proceder
        if (!this.IdEdicion) {
          alert('No se ha seleccionado un curso para editar');
          return;
        }
  
        //Verifica si el formulario es válido 
        if (!this.checkFormValidity()) {
          alert('Formulario no válido. Revise los campos.');
          return;
        }
        // se realiza la edicion del curso, si el formulario es válido
        const DesEditar = this.nameEditar
        const EstadoEditar = this.SelectEstado
        const HorasEditar = this.nameEditarHoras
        const TipoDiploma = this.SelectDiploma
        const nombreEditar = this.formData.NombreCurso 
        const identiEditar = this.formData.Cedula
        const texto1Editar = this.formData.Texto1
        const texto2Editar = this.formData.Texto2
        const texto3Editar = this.formData.Texto3
        const fechaEditar = this.formData.Fecha  
          
        // Llamada de la API para editar el curso
        axios.put(`http://localhost:3000/cursos/${this.IdEdicion}`,{
          DesEditar,
          EstadoEditar,
          HorasEditar,
          TipoDiploma,
          nombreEditar,
          identiEditar,
          texto1Editar,
          texto2Editar,
          texto3Editar,
          fechaEditar
        })
        .then (response =>{
          if(response.data.ok){
            alert("Registro editado con éxito");
            this.getCursos(); //Actualiza la lista de cursos
            this.handleSubmit(); //Finaliza la edición y cierra el modal             
          }else{
            //console.log("error edicion",response)
            alert("Error al tratar de editar el registro");
          }
        })
        .catch (e => console.log("esta errado:",e))
      },    
      handleSubmit() {      
        // Agrega el nombre a "submittedNames"
        this.submittedNames.push(this.name);
  
        // cierra modal manualmente
        this.$nextTick(() => {
          this.$bvModal.hide('modalEdicion')
        })
      },   
      convertirADataURL(imgSrc, callback) {
        // Crear un objeto FileReader
        const reader = new FileReader();
        // Obtener el archivo usando fetch (ya que imgSrc es una ruta local)
        fetch(imgSrc)
          .then((response) => response.blob()) // Convertir a blob
          .then((blob) => {
            // Leer el blob como Data URL
            reader.readAsDataURL(blob);
            reader.onload = () => callback(reader.result); // Llamar al callback con el resultado base64
          })
          .catch(() => callback(null));
      },    
      convertirImagenesPorDefecto() {
        // Recorre el arreglo y convierte cada imagen a base64
        this.optionsDiploma.forEach((option) => {
          if (option.imgSrc) {
            this.convertirADataURL(option.imgSrc, (dataURL) => {
              option.img = dataURL; // Asigna la imagen en base64 al objeto de opción
            });
          }
        });
      },
      getSelectedImage() {
        const selectedOption = this.optionsDiploma.find(option => option.value === this.SelectDiploma);
        return selectedOption ? selectedOption.img : '';
      },
      cargaImagen(event) {
        const file = event.target.files[0];
        if (file) {
          const reader = new FileReader(); //(FileReader) convierte la imagen en base 64
          reader.onload = (e) => {
            const newImageUrl = e.target.result;
            this.imgSelect = newImageUrl;
            this.optionsDiploma.push({ value: newImageUrl, text: file.name });
            this.SelectDiploma = newImageUrl;
          };
          reader.readAsDataURL(file);
        }
      },
    },
    computed:{
      ValidarInputNumerico() {
        return this.id && !isNaN(this.id);
      },
      ...mapState(['participante','curso']),
      listarcurso: function() {
          //return this.participante.filter((item) => item.nota > 4);
          return this.curso.filter((item) => item.value != null);
      },
      imagenSelecciona() {
        //Verifica si SelectDiploma es una (data URL) de una imagen cargada
        if (this.SelectDiploma && this.SelectDiploma.startsWith("data:imagen/")){        
          return this.SelectDiploma;
        }
        //Mapea el valor de SelectDploma a las rutas locales de la imagen
        const rutasImagenes = {
          img1: require("../assets/img1.png"),
          img2: require("../assets/img2.jpeg"),
          img3: require("../assets/img3.jpg"),
          img4: require("../assets/logo.png"),
        };
        return rutasImagenes[this.SelectDiploma] || null;
      },
    },
      props: {
      msg: String
    }
  }
  </script>
  <!--******************************************************STYLE*********************************************************************** */-->
  <style>
  
  table.scrolling .w3 {
      width: 4.5em;
      min-width: 4.5em;
      max-width: 4.5em;
    height:2em; 
  }
  table.scrolling .w4 {
      width: 43em;
      min-width: 43em;
      max-width: 43em;
    height:2em; 
  }
  table.scrolling .NroId {
      width: 6em;
      min-width: 6em;
      max-width: 6em;
  }
  table.scrolling .nombres {
      width: 9em;
      min-width: 9em;
      max-width: 9em;
  }
  table.scrolling .action {
      width: 12em;
      min-width: 12em;
      max-width: 12em;
  }
  
  
  table.scrolling .botones {
  column-span: 3;
  }
  table.scrolling tfoot tr th {
      width: 130em;
      min-width: 130em;
      max-width: 130em;
  }
  table.freezeFirstColumn thead tr,
  table.freezeFirstColumn tbody tr {
      display: block;
      width: min-content;
  }
  table.freezeFirstColumn thead td:first-child,
  table.freezeFirstColumn tbody td:first-child,
  table.freezeFirstColumn thead th:first-child,
  table.freezeFirstColumn tbody th:first-child {
      position: sticky;
      position: -webkit-sticky;
      left: 0;
  }
  * {
      font-family: sans-serif;
  }
  .box {
      clear: both;
      padding: 0;
      min-height: 300px;
      height: 40vh;
    /* width: 100vh; */
      margin-left: auto;
      margin-right: auto;
      overflow: hidden;
  }
  @font-face {
      font-family: FontAwesome;
      src: url(https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/fonts/fontawesome-webfont.woff);
  }
  .fa {
      font-family: FontAwesome;
  }
  </style>
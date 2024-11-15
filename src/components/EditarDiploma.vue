<template>
  <b-form>
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
      <!-- Selección de Fondo imagen -->
    <main class="row">
      <!---Input Descripción del evento-->
      <div class="row g-2">
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
          ></b-form-input>
        </b-form-group>
        <!-- seleccion Estado curso -->
        <b-form-group class="col-sm m-2"
          id="inputEstadoCurso"
          label="Estado Curso:"
          label-for="estado"
          invalid-feedback="Debe seleccionar el estado del curso"
          :state="SeleccionCursoEstado"
          > 
          <b-form-select
          class="form-select"
            id="estado"
            v-model="SelectEstado" 
            label="Seleccione Estado:" 
            descripcion="estado"
            :options="options" 
            :state="SeleccionCursoEstado" 
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
          <div v-if ="SelectDiploma === 'img1'">
          <img 
            class="card-img" 
            src="../assets/img1.png" 
            alt="certificado"
            >              
        </div>
        <div v-if ="SelectDiploma === 'img2'">
          <img 
          class="card-img" 
          src="../assets/img2.jpeg" 
          alt="certificado"
          >            
        </div>
        <div v-if ="SelectDiploma === 'img3'">
          <img 
          class="card-img" 
          src="../assets/img3.jpg" 
          alt="certificado"
          >            
        </div><!--Asegura que solo se muestra la imagen seleccionada desde el input de tipo file si SelectDiploma es una URL de datos (Data URL).-->
        <div v-if="SelectDiploma && SelectDiploma.startsWith('data:image/')">
          <img 
          class="card-img" 
          :src="SelectDiploma" 
          alt="certificado seleccionada" 
          >
        </div>
        
        <!-- Muestra la imagen de fondo -->
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
      <!-- ********** Formulario**********-->
      <div class="col">
      <form ref="form" 
          @submit.stop.prevent="handleSubmit"
          >
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
        <div v-if="selectedText === 'id'">
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
              id="colorId"
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
              id="fontSizeId"
              v-model="formData.TamañoCedula"
              >
            </div>
          <div class="form-group">
            <label for="positionId">Posición:</label>
            <input
              class="form-range"
              type="range"
              id="positionId"
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
</template>


<script>
import axios from 'axios';

export default {
    name:'EditarDiploma',
  data() {
    return {
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
      NuevoCurso:'',
      errors: [],
      name: null,
      age: null,
      movie: null,
      cursovirtual: null,
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
      options: [
        { value: null, text: 'Por favor seleccione una opción' },
        { value: '1', text: 'ACTIVO' },
        { value: '0', text: 'INACTIVO' }
      ],  
      optionsDiploma:[
        { value: null, text: "Por favor seleccione un diploma"},
        { value: 'img1', text: 'NORMAL' },
        { value: 'img2', text: 'HOMIL' },
        { value: 'img3', text: 'TOMA MUESTRAS DE CITOLOGIA' },
        { value: 'img4', text: 'CONGRESO CLAHT' }
          
      ],
      opcionConfiTexto:[
        {value: null, text: "Seleccione Texto"},
        {value:"nombre",text: "Nombre"},
        {value:"id",text: "Identificación"},
        {value:"texto1",text: "Texto1"},
        {value:"texto2",text: "Texto2"},
        {value:"texto3",text: "Texto3"},
        {value:"fecha",text:"Fecha"},
       ],
    }
  },
   
  methods: {
    submitForm() {
      console.log("Envia:", this.formData);
      //console.log(FondoDiploma);
    },     
    
    convertirADataURL(imagen) {
      const xhr = new XMLHttpRequest();
      xhr.open('GET', imagen, true);
      xhr.responseType = 'blob'; // Obtener como blob (Binary Large Object) es un objeto de JavaScript que representa datos binarios sin formato.

      xhr.onload = (e) => {
        if (this.status === 200) {
          const reader = new FileReader(); //(FileReader) convierte la imagen en base 64
          reader.onloadend = () => {
            this.formData.FondoDiploma.push(reader.result); // Guardar en formato base64
          };
          reader.readAsDataURL(xhr.response); // Convertir a base64
        }
      };
      xhr.send();
    },
    cargaImagen(event) {
      const file = event.target.files[0];
      if (file) {
        const reader = new FileReader(); //(FileReader) convierte la imagen en base 64
        reader.onload = (e) => {
          const newImageUrl = e.target.result;
          this.imgSelect = newImageUrl;
          this.optionsDiploma.push({ 
            value: newImageUrl, text: file.name 
          });
          this.SelectDiploma = newImageUrl;         
        };
        reader.readAsDataURL(file);
      }
    },
    
    resetModal() {
      this.name = ''
      this.nameState = null
    },
    handleSubmit() {
      // Exit when the form isn't valid
      if (!this.checkFormValidity()) {
        return;
      }
      // Push the name to submitted names
      this.submittedNames.push(this.name);
      // Hide the modal manually
      this.$nextTick(() => {
        this.$bvModal.hide('modalEdicion');
      });
    },
    handleOk(bvModalEvt) {
      // se realiza la edicion del curso
      const DesEditar = this.nameEditar
      const EstadoEditar = this.SelectEstado
      const HorasEditar = this.nameEditarHoras
      const TipoDiploma = this.SelectDiploma

        // Prevent modal from closing
        bvModalEvt.preventDefault()
        // Trigger submit handler
        this.handleSubmit()

        if (this.valid){
          axios.put(`http://localhost:3000/cursos/${this.IdEdicion}`,
          {
            DesEditar,
            EstadoEditar,
            HorasEditar,
            TipoDiploma
          })
          .then (response =>{
            if(response.data.ok){
              alert("Registro editado con éxito");
              this.getCursos();
            }else{
              alert("Error al tratar de editar el registro");
            }
          })
          .catch (e => console.log("esta errado:",e))
        }
      },
      checkFormValidity() {
      const valid = this.$refs.form.checkValidity();
      this.nameState = valid;
      this.SeleccionCursoEstado = valid; 
      this.SeleccionDiploma = valid;
      this.valid = valid;
      return valid;
    }, 
  },
};
</script>

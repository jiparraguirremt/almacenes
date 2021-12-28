<template>
  <div class="orden-page">
    <b-row>
      <b-col>
        <h2 class="page-title">Orden de <span class="fw-semi-bold">compra</span></h2>
      </b-col>
    </b-row>
    <b-row>
      <b-col>
        <Widget
          customHeader
        >
        <h5 v-show="!showTable" class="page-title">Generar <span class="fw-semi-bold">orden de compra</span></h5>
        <div>
          <b-form @submit.prevent="postOrden" @reset="onReset" v-if="show">
            <b-row>
                <b-col cols="12" md="2">
                  <b-form-group id="input-group-codigo" label="Código:" label-for="input-codigo">
                  <b-form-input id="input-codigo" v-model="formOrden.codigo" placeholder="Código de OC"></b-form-input>
                  </b-form-group>
                </b-col>
                 <b-col cols="12" md="8">
                   <b-form-group id="input-group-usuario" label="Usuario:" label-for="input-usuario">
                  <b-form-input id="input-usuario" v-model="formOrden.usuario" ></b-form-input>
                  </b-form-group>
                 </b-col>
                 <b-col cols="12" md="2">
                  <b-form-group id="input-group-fecha" label="Fecha" label-for="input-admincode">
                  <div>
                    <date-picker v-model="formOrden.fecha" valueType="format" placeholder="Seleccione una fecha">
                    </date-picker>
                  </div>
                  </b-form-group>
                </b-col>
            </b-row>
            <b-row>
              <b-col cols="12" md="3">
                <b-form-group id="input-group-proveedor" label="Proveedor:" label-for="input-proveedor">
                  <ejs-autocomplete :dataSource="proveedoresArr" :fields="dataFields" placeholder="Seleccione Proveedor" :highlight="true" v-model="formOrden.proveedor">
                  </ejs-autocomplete>
                </b-form-group>
              </b-col>
              <b-col cols="12" md="3">
                <b-form-group id="input-group-areaSolicitante" label="Área Solicitante:" label-for="input-areaSolicitante">
                  <b-form-select id="input-areaSolicitante" v-model="formOrden.areaSolicitante" :options="solicitantelist"></b-form-select>
                </b-form-group>
              </b-col>
              <b-col cols="12" md="3">
                <b-form-group id="input-group-facturar" label="Factura A:" label-for="input-facturar">
                  <b-form-select id="input-facturar" v-model="formOrden.facturar" :options="facturarList" @change="matches"></b-form-select>
                </b-form-group>
              </b-col>
              <b-col cols="12" md="3">
                <b-form-group id="input-group-NIT" label="NIT:" label-for="input-NIT">
                  <b-form-input id="input-NIT" v-model="formOrden.NIT" placeholder="" disabled ></b-form-input>
                </b-form-group>
              </b-col>
            </b-row>
            <b-row>
              <b-col cols="12" md="3">
                <b-form-group id="input-group-pago" label="Tipo de Pago:" label-for="input-pago">
                  <b-form-select id="input-pago" v-model="formOrden.pago" :options="pagoList" ></b-form-select>
                </b-form-group>
              </b-col>
              <b-col cols="12" md="3">
                  <b-form-group id="input-group-descripcion" label="Descripción:" label-for="input-descripcion">
                  <b-form-input id="input-descripcion" v-model="formOrden.descripcion" ></b-form-input>
                  </b-form-group>
              </b-col>
              <b-col cols="12" md="3">
                <b-form-group id="input-group-moneda" label="Moneda:" label-for="input-moneda">
                  <b-form-select id="input-moneda" v-model="formOrden.moneda" :options="monedaList" ></b-form-select>
                </b-form-group>
              </b-col>
              <b-col cols="12" md="3">
                <b-form-group id="input-group-entrega" label="Tiempo de entrega:" label-for="input-entrega">
                  <b-form-select id="input-entrega" v-model="formOrden.entrega" :options="entregaList" ></b-form-select>
                </b-form-group>
              </b-col>
            </b-row>
            <hr>
            <b-row>
              <b-col cols="12" md="10">
              </b-col>
              <b-col cols="12" md="2" class="btn btn-i-success pull-left">
                <b-button variant="gray" size="sm"  @click="openModalAdd()">
                      <b-icon  icon="plus-square" aria-hidden="true"></b-icon>
                </b-button>
              </b-col>
            </b-row>
            <div>
            <div>
              <b-modal :id="'modal-add-item'" title="Agregar Item" header-bg-variant="dark" body-bg-variant="light" header-text-variant="white" :hide-footer="true" >
                <b-form>
                  
                  <b-form-group id="input-group-SKU" label="SKU:" label-for="input-SKU">
                      <b-form-input id="input-SKU" v-model="formProducto.SKU" placeholder="Ingrese el SKU del producto" @change="descripProducto"></b-form-input>
                  </b-form-group> 
                  <b-form-group id="input-group-codFabrica" label="Código de Fábrica:" label-for="input-codFabrica">
                        <b-form-input id="input-codFabrica" v-model="formProducto.codFabrica" disabled></b-form-input>
                  </b-form-group>
                  <b-form-group id="input-group-marca" label="Marca:" label-for="input-marca">
                        <b-form-input id="input-marca" v-model="formProducto.marca" disabled></b-form-input>
                  </b-form-group>
                  <b-form-group id="input-group-descripcion" label="Descripción:" label-for="input-descripcion">
                        <b-form-input id="input-descripcion" v-model="formProducto.descripcion" disabled></b-form-input>
                  </b-form-group>
                  <b-row>
                    <b-col cols="12" md="6">
                      <b-form-group id="input-group-unidad" label="Unidad:" label-for="input-unidad">
                        <b-form-input id="input-unidad" v-model="formProducto.unidad" placeholder="Ingrese unidad" class="text-right"></b-form-input>
                      </b-form-group>
                    </b-col>
                    <b-col cols="12" md="6">
                      <b-form-group id="input-group-cantidad" label="Cantidad:" label-for="input-cantidad">
                        <b-form-input id="input-cantidad" v-model="formProducto.cantidad" placeholder="Ingrese Cantidad" class="text-right" @change="setearPreciot"></b-form-input>
                      </b-form-group>
                    </b-col>
                  </b-row>
                  <b-row>
                    <b-col cols="12" md="6">
                      <b-form-group id="input-group-precioU" label="Precio Unitario:" label-for="input-precioU">
                        <b-form-input id="input-precioU" v-model="formProducto.precioU" disabled class="text-right"></b-form-input>
                      </b-form-group>
                    </b-col>
                    <b-col cols="12" md="6">
                      <b-form-group id="input-group-precioT" label="Precio Total:" label-for="input-precioT">
                        <b-form-input id="input-precioT" v-model="formProducto.precioT" disabled class="text-right"></b-form-input>
                      </b-form-group>
                    </b-col>
                  </b-row>
                  <div class="data-loader"  v-if="loadingModal">
                      <em class="la la-spinner la-spin"></em>
                  </div>
                  <b-button  variant="inverse" class="mr-xs" style="margin-top:28px;margin-left:380px" size="xl" @click="AgregarItem" >Agregar</b-button>
                </b-form>
              </b-modal>
            </div>
            <div class="table-resposive">
              <table  class="table table-hover">
                <thead>
                <tr>
                  <th id="item" class="hidden-sm-down">Item</th>
                  <th id="sku" class="hidden-sm-down">SKU</th>
                  <th id="cod" class="hidden-sm-down">Cod F.</th>
                  <th id="marca" class="hidden-sm-down">Marca</th>
                  <th id="decripcion" >Descripción</th>
                  <th id="unidad" class="hidden-sm-down">Unidad</th> 
                  <th id="cantidad" class="hidden-sm-down">Cantidad</th> 
                  <th id="precioU" class="hidden-sm-down">Precio Unitario</th> 
                  <th id="precioT" class="hidden-sm-down">Precio T. Bs</th> 
                </tr>
              </thead>
              <tbody>
                <tr v-for="row in productoGeneral" :key="row.productoGeneral">
                  <td class="text-center"><span ><b-icon  icon="file-minus" aria-hidden="true" style="cursor: pointer;" @click="quitarItem(i)" ></b-icon></span></td>
                  <td>{{row.SKU}}</td>
                  <td>{{row.codFabrica}}</td>
                  <td>{{row.marca}}</td>
                  <td>{{row.descripcion}}</td>
                  <td>{{row.unidad}}</td>
                  <td>{{row.cantidad}}</td>
                  <td>{{row.precioU}}</td>
                  <td>{{row.precioT}}</td>
                </tr>
              </tbody>
              </table>
            </div>
            <b-row>
              <b-col cols="12" md="10">
              </b-col>
              <b-col cols="12" md="2">
                <b-form-group id="input-group-subtotal" label="Sub Total:" label-for="input-subtotal">
                  <b-form-input id="input-subtotal" v-model="formOrden.subtotal" class="text-right" disabled></b-form-input>
                </b-form-group>
              </b-col>
            </b-row>
            <b-row>
              <b-col cols="12" md="10">
              </b-col>
              <b-col cols="12" md="2">
                <b-form-group id="input-group-descuento" label="Descuento:" label-for="input-descuento">
                  <b-form-input id="input-descuento" v-model="formOrden.descuento" class="text-right" @keyup="setearTotal"></b-form-input>
                </b-form-group>
              </b-col>
            </b-row>
            <b-row>
              <b-col cols="12" md="10">
              </b-col>
              <b-col cols="12" md="2">
                <b-form-group id="input-group-totales" label="Totales:" label-for="input-totales">
                  <b-form-input id="input-totales" v-model="formOrden.totales" class="text-right" disabled></b-form-input>
                </b-form-group>
              </b-col>
            </b-row>
          </div>
          <br>
          <div class="clearfix">
            <div class="float-right">
              <b-button variant="default" class="mr-3" size="sm" >Enviar</b-button>
              <b-button variant="inverse" class="mr-xs" size="sm" @click="postOrden">Guardar</b-button>
            </div>
          </div>
          </b-form>
          
        </div>
        </Widget>
      </b-col>
    </b-row>
  </div>
</template>
<script>
import Vue from 'vue'
import config from '../../../config'
import Widget from '@/components/Widget/Widget'
import Sparklines from '../../../components/Sparklines/Sparklines'
import { AutoCompletePlugin } from "@syncfusion/ej2-vue-dropdowns";
Vue.use(AutoCompletePlugin);
import DatePicker from 'vue2-datepicker'
import 'vue2-datepicker/index.css'
import vue2Dropzone from 'vue2-dropzone'
import 'vue2-dropzone/dist/vue2Dropzone.min.css'
import { BootstrapVue, BootstrapVueIcons } from 'bootstrap-vue'
import VueHtml2pdf from 'vue-html2pdf'
import html2pdf from 'html2pdf.js'
import moment from 'moment'
moment.locale('es')
Vue.use(BootstrapVue)
Vue.use(BootstrapVueIcons)

export default {
  name: 'GenerarOrden',
  components: {
    Widget,
    Sparklines,
    DatePicker,
    VueHtml2pdf,
    vueDropzone: vue2Dropzone,
  },
  data() {
    return {
      ip: config.ip,
      user:{},
      id_user:null,
      usuario:null,
      page: 1,
      perpage: 10,
      allRows: 0,
      nit:[],
      nitresult:null,
      selectedFile: null,
      loadingDni: false,
      loadingTable: true,
      showTable: false,
      showRegister: true,
      showSearch: false,
      loadingForm: false,
      loadingModal: false,
      expedientes: [],
      ordenes: [],
      dataFields:{value:'text'},
      productoDetalle:[],
      typeDoc:[{text:'La Paz Murillo',value:1},{text:'Almacen 2',value:2}],
      proveedoresArr:[],
      guiaFiltered:[],
      solicitantelist:[],
      facturarList:[],
      origenList:[{text:'SANTA CRUZ',value:1}],
      pagoList:[],
      monedaList:[],
      entregaList:[],
      productoGeneral:[],
      typeInput: null,
      searchInput: '',
      dropzoneOptions: {
        url: 'https://httpbin.org/post',
        thumbnailWidth: 150,
        maxFilesize: 500,
        maxFiles: 4,
        acceptedFiles:".pdf, .txt, .jpg, .png, .docx, .doc, .xls, .xlsx, .jpeg, .ppt, .pptx, .dwg, .dxf",
        headers: { "My-Awesome-Header": "header value" }
      },
      formOrden: {
        codigo:null,
        usuario:null,
        producto:[],
        proveedor: null,
        areaSolicitante:null,
        facturar: null,
        NIT:null,
        descripcion: null,
        proyecto: null,
        nota: null,
        origen: null,
        fecha: null,
        pago: null,
        moneda: null,
        entrega: null,
        subtotal:0.00,
        descuento:0.00,
        totales:0.00
      },
      formProducto: {
        SKU: null,
        codFabrica:null,
        marca: null,
        descripcion:null,
        unidad: null,
        cantidad: 0,
        precioU: 0.00,
        precioT: 0.00,
      },
      formSearch: {
        searchInput: null,
        typeInput: null
      },
      htmlToPdfOptions: {
          margin: 0,
      
          filename: `hehehe.pdf`,
      
          image: {
              type: 'jpeg', 
              quality: 0.98
          },
      
          enableLinks: false,
      
          html2canvas: {
              scale: 1,
              useCORS: true
          },
      
          jsPDF: {
              unit: 'in',
              format: 'a4',
              orientation: 'portrait'
          }
      },
      show: true, 
      optionsPerPage: [
        {text: '10',value: 10},
        {text: '20', value: 20},
        {text: '30',value: 30}
      ],
      tiposDoc: [
        { text: 'Seleccionar', value: null },
        { text: 'Carta', value: 1 },
        { text: 'Escritos', value: 2 },  
        { text: 'Proyecto', value: 3 }, 
        { text: 'Otros', value: 4 }
      ],
    };
  },
  methods: {
    afterComplete(file) {
      // console.log(file);
      this.form.files.push(file);
    },
    onFileSelected(event){
      // console.log(event);
      this.form.files.push(event.target.files[0]);
      // this.selectedFile = event.target.files[0];
    },
    format_date(value){
      if (value) {
        return moment(String(value)).format('L')
      }
    },
    format_file(value){
      if(value){
        return value.c_archivo_exp
      }
    },
    searchData(){
      if(this.typeInput === "todo"){
        this.load();
      }
      else if(this.searchInput ==null || this.searchInput == "" ||
      this.typeInput == null || this.typeInput == ""){
        this.$toasted.error('Ingrese los campos de búsqueda', {
          duration: 1500,
          position: 'top-center'
        });
      }
      else{
        this.loadingTable = true;
        this.showTable = false;
        axios.post(config.hostname+'expediente/buscar_expediente',
        {
          "search": this.searchInput,
          "origen": this.typeInput,
          "id_area_trabajo":1,
          "page":1,
          "perpage":this.perpage
        })
        .then( (response) =>{
          if(response.data.data.length == 0){
            this.$toasted.error('No se encontraron resultados', {
              duration: 1500,
              position: 'top-center'
            });
            this.loadingTable = false;
            this.showTable = true;
          }
          else{
            this.$toasted.success('Resultados', {
              duration: 1500,
              position: 'top-center'
            });
            this.expedientes = response.data.data;
            this.allRows = Number(response.data.total);
            this.loadingTable = false;
            this.showTable = true;
            this.formSearch.typeInput = this.typeInput;
            this.formSearch.searchInput = this.searchInput;
            this.showSearch = true;
          }
          
        })
        .catch( (error) =>{
          console.log(error);
        });
      }
    },
    load(){
      this.loadingTable = true;
      this.showTable = false;
      axios.get(config.hostname+'expediente/obtener_expediente_area/1/'+this.page+'/'+this.perpage+'/'+this.id_user)
      .then( (response) =>{
        this.loadingTable = false;
        this.showTable = true;
        this.expedientes = response.data.data;
        this.allRows = Number(response.data.total);
        this.showSearch = false;
      })
      .catch( (error) =>{
        this.loadingTable = false;
        this.showTable = true;
        console.log(error);
      });
    },
    exportToPDF () {
				html2pdf(this.$refs.document, {
					margin: 1,
					filename: 'document.pdf',
					image: { type: 'jpeg', quality: 0.98 },
					html2canvas: { dpi: 192, letterRendering: true },
					jsPDF: { unit: 'in', format: 'letter', orientation: 'landscape' }
				})
			},
    loadPerPage(perpage){
      if(this.showSearch == true){
        this.page = 1;
        axios.post(config.hostname+'expediente/buscar_expediente',
        {
          "search": this.formSearch.searchInput,
          "origen": this.formSearch.typeInput,
          "id_area_trabajo":1,
          "page":this.page,
          "perpage":perpage
        })
        .then( (response) =>{
          if(response.data.data.length == 0){
            this.$toasted.error('No se encontraron resultados', {
              duration: 1500,
              position: 'top-center'
            });
          }
          else{
            setTimeout(function(){document.body.scrollTop = 0; }, 50);
            this.expedientes = response.data.data;
            this.allRows = Number(response.data.total);
          }
        })
        .catch( (error) =>{
          console.log(error);
        });
      }else{
        this.page = 1;
        axios.get(config.hostname+'expediente/obtener_expediente_area/1/1/'+perpage+'/'+this.id_user)
        .then( (response) =>{
          this.expedientes = response.data.data;
        })
        .catch( (error) =>{
          console.log(error);
        });
      }
    },
    loadPage(page){
      if(this.showSearch == true){
        axios.post(config.hostname+'expediente/buscar_expediente',
        {
          "search": this.formSearch.searchInput,
          "origen": this.formSearch.typeInput,
          "id_area_trabajo":1,
          "page":page,
          "perpage":this.perpage
        })
        .then( (response) =>{
          if(response.data.data.length == 0){
            this.$toasted.error('No se encontraron resultados', {
              duration: 1500,
              position: 'top-center'
            });
          }
          else{
            setTimeout(function(){document.body.scrollTop = 0; }, 50);
            this.expedientes = response.data.data;
            this.allRows = Number(response.data.total);
          }
        })
        .catch( (error) =>{
          console.log(error);
        });
      }else{
        axios.get(config.hostname+'expediente/obtener_expediente_area/1/'+page+'/'+this.perpage+'/'+this.id_user)
        .then( (response) =>{
          setTimeout(function(){document.body.scrollTop = 0; }, 50);
          this.expedientes = response.data.data;
          this.allRows = Number(response.data.total);
        })
        .catch( (error) =>{
          console.log(error);
        });
      }
    },
    removeAllFiles() {
      this.form.files = [];
      this.$refs.dropzoneCreate.removeAllFiles();
    },
    register(){
      this.form.codeDoc = null;
      this.form.timeDoc = null;
      this.form.checked = null;
      this.form.tipoDoc = null;
      this.form.issueDoc = null;
      this.form.fileDoc = null;
      this.showTable = false;
      this.showRegister = true
    },
    openModalDelete(id) {
      this.$root.$emit('bv::show::modal', 'modal-delete-'+id)
    },
    hideModalDelete(id) {
      this.$root.$emit('bv::hide::modal', 'modal-delete-'+id);
    },
    openModalAdd() {
      this.$root.$emit('bv::show::modal', 'modal-add-item');

    },
    hideModalEdit(id) {
      this.$root.$emit('bv::hide::modal', 'modal-edit-'+id);
    },
    deleteRow(id){
      this.loadingModal = true;
      axios.put(config.hostname+'expediente/eliminar_expediente', 
        {
          "id_expediente": id
        },
        {
          headers: {
            'token':window.localStorage.getItem('token')
          }
        })
      .then( (response) =>{
        if(response.data.status === 'success'){
          if(config.hosts == true){
            axios.put(config.hostname_bd+'expediente/eliminar_expediente', 
            {
              "id_expediente": id
            },
            {
              headers: {
                'token':window.localStorage.getItem('token')
              }
            }).then( (response) =>{
              if(response.data.status === 'success'){
                this.loadingModal = false;
                this.$toasted.success(response.data.message, {
                  duration: 1500,
                  position: 'top-center'
                });
                this.load();
                this.hideModalDelete(id);
              }
              else{
                this.loadingModal = false;
                this.$toasted.error(response.data.message, {
                  duration: 1500,
                  position: 'top-center'
                });
              }
            })
          }
          else{
            this.loadingModal = false;
            this.$toasted.success(response.data.message, {
              duration: 1500,
              position: 'top-center'
            });
            this.load();
            this.hideModalDelete(id);
          }
        }
        else{
          this.loadingModal = false;
          this.$toasted.error(response.data.message, {
            duration: 1500,
            position: 'top-center'
          });
        }
      })
      .catch((error) =>{
        this.$toasted.error('Error', {
            duration: 1500,
            position: 'top-center'
        });
        this.loadingModal = false;
      });
    },
    editRow(id){
      if(
      this.form.codeDoc == null || this.form.codeDoc == "" ||
      this.form.tipoDoc == null || this.form.tipoDoc == "" ||
      this.form.issueDoc == null || this.form.issueDoc == "" ||
      this.form.timeDoc == null || this.form.timeDoc == ""){
        this.$toasted.error('Rellene todos los campos requeridos', {
          duration: 1000,
          position: 'top-center'
        });
      }
      else{
        this.$toasted.error('¿Está seguro de modificar el expediente?', {
          position: 'top-center',
          action : [
            {
              text : 'Si',
              onClick : (e, toastObject) => {
                toastObject.goAway(0);
                this.loadingModal = true;
                axios.put(config.hostname+'expediente/actualizar_expediente', {
                  "id_expediente":id,
                  "id_administrado":1, 
                  "id_tipo_doc_exp":this.form.tipoDoc,
                  "id_estado_exp":1, 
                  "id_area_trabajo":1, 
                  "c_cod_exp": this.form.codeDoc, 
                  "c_asunto_exp": this.form.issueDoc, 
                  "d_tramite_exp": this.form.timeDoc, 
                  "id_usuario_c": 1, 
                  "d_c_exp": this.form.timeDoc, 
                  "id_usuario_u":1, 
                  "d_u_exp": this.form.timeDoc,
                  "id_usuario_h":1,
                  "c_cod_hrc": this.form.hrcDoc,
                  "d_h_exp": this.form.timeDoc,
                  "b_exp":true,
                  "b_obs_exp":true
                },{})
                .then( (response) =>{
                  if(response.data.status === 'success'){
                    if(config.hosts == true){
                      axios.put(config.hostname_bd+'expediente/actualizar_expediente', {
                      "id_expediente":id,
                      "id_administrado":1, 
                      "id_tipo_doc_exp":this.form.tipoDoc,
                      "id_estado_exp":1, 
                      "id_area_trabajo":1, 
                      "c_cod_exp": this.form.codeDoc, 
                      "c_asunto_exp": this.form.issueDoc, 
                      "d_tramite_exp": this.form.timeDoc, 
                      "id_usuario_c": 1, 
                      "d_c_exp": this.form.timeDoc, 
                      "id_usuario_u":1, 
                      "d_u_exp": this.form.timeDoc,
                      "id_usuario_h":1,
                      "d_h_exp": this.form.timeDoc,
                      "b_exp":true,
                      "b_obs_exp":true
                      },{}).then( (response) =>{
                        if(response.data.status === 'success'){
                          this.loadingModal = false;
                          this.$toasted.success(response.data.message, {
                            duration: 1500,
                            position: 'top-center'
                          });
                          this.load();
                          this.hideModalEdit(id);
                        }
                        else{
                          this.loadingModal = false;
                          this.$toasted.error(response.data.message, {
                            duration: 1500,
                            position: 'top-center'
                          });
                          this.hideModalEdit(id);
                        }
                      });
                    }
                    else{
                      this.loadingModal = false;
                      this.$toasted.success(response.data.message, {
                        duration: 1500,
                        position: 'top-center'
                      });
                      this.load();
                      this.hideModalEdit(id);
                    }
                  }
                  else{
                    this.loadingModal = false;
                    this.$toasted.error(response.data.message, {
                      duration: 1500,
                      position: 'top-center'
                    });
                    this.hideModalEdit(id);
                  }
                })
                .catch((error) =>{
                  this.loadingModal = false;
                  this.$toasted.error('Error', {
                      duration: 1500,
                      position: 'top-center'
                  });
                });
              }
            },
            {
              text : 'No',
              onClick : (e, toastObject) => {
                this.hideModalEdit(id); 
                toastObject.goAway(0);
              }
            }
          ]
        });
      }
    },
    onSubmit() {
      
      // if(
      // // this.form.codeDoc == null || this.form.codeDoc == "" ||
      // this.form.adminCode == null || this.form.adminCode == "" || 
      // this.form.tipoDoc == null || this.form.tipoDoc == "" ||
      // this.form.issueDoc == null || this.form.issueDoc == "" ||
      // this.form.timeDoc == null || this.form.timeDoc == "" || this.form.files.length == 0
      // || this.form.files == []){
      //   this.$toasted.error('Rellene todos los campos requeridos', {
      //     duration: 1000,
      //     position: 'top-center'
      //   });
      // }
      // else{
      //   this.loadingForm = true;
      //   var fd = new FormData();
      //   this.form.files.forEach(
      //     e=> fd.append('files_expediente',e),
      //   );
      //   fd.append('id_administrado',this.form.adminCode);
      //   fd.append('id_tipo_doc_exp',this.form.tipoDoc);
      //   fd.append('id_estado_exp',1);
      //   fd.append('id_area_trabajo',1);
      //   // fd.append('c_cod_exp',this.form.codeDoc);
      //   fd.append('c_cod_hrc',this.form.hrcDoc);
      //   fd.append('c_asunto_exp',this.form.issueDoc);
      //   fd.append('d_tramite_exp',this.form.timeDoc);
      //   fd.append('id_usuario_c',1);
      //   fd.append('d_c_exp',this.form.timeDoc);
      //   fd.append('id_usuario_u',1);
      //   fd.append('d_u_exp',this.form.timeDoc);
      //   fd.append('id_usuario_h',1);
      //   fd.append('d_h_exp',this.form.timeDoc);
      //   fd.append('b_exp',true);
      //   fd.append('b_obs_exp',true);
      //   axios.post(config.hostname+'expediente/registrar_expediente',fd,
      //   {
      //     headers: {
      //       'Content-Type':'multipart/form-data; '
      //     }
      //   })
      //   .then( (response) =>{
      //     if(response.data.status === 'success'){
      //       if(config.hosts == true){
      //         axios.post(config.hostname_bd+'expediente/registrar_expediente',fd,
      //         {
      //           headers: {
      //             'Content-Type':'multipart/form-data; '
      //           }
      //         }).then( (response) =>{
      //           if(response.data.status === 'success'){
      //               this.loadingForm = false;
      //               this.form.dniDoc = '';
      //               this.form.adminCode = '';
      //               this.form.nameAdmin = '';
      //               this.form.hrcDoc = '';
      //               this.form.dniDoc = '';
      //               this.form.timeDoc = null;
      //               this.form.issueDoc = '';
      //               this.form.tipoDoc= null;
      //               this.form.files = [];
      //               this.$toasted.success(response.data.message, {
      //                 duration: 1500,
      //                 position: 'top-center'
      //               });
      //               this.load();
      //               this.showTable = true;
      //               this.showRegister = false;
      //               this.$refs.dropzoneCreate.removeAllFiles();
      //           }
      //           else{
      //             this.loadingForm = false;
      //             this.$toasted.error(response.data.message, {
      //                 duration: 1500,
      //                 position: 'top-center'
      //               });
      //             }
      //         })
      //         .catch( (error) =>{
      //           this.loadingForm = false;
      //           this.$toasted.error('Error', {
      //               duration: 1500,
      //               position: 'top-center'
      //           });
      //         });
      //       }
      //       else{
      //         this.loadingForm = false;
      //         this.form.dniDoc = '';
      //         this.form.adminCode = '';
      //         this.form.nameAdmin = '';
      //         this.form.hrcDoc = '';
      //         this.form.dniDoc = '';
      //         this.form.timeDoc = null;
      //         this.form.issueDoc = '';
      //         this.form.tipoDoc= null;
      //         this.form.files = [];
      //         this.$toasted.success(response.data.message, {
      //           duration: 1500,
      //           position: 'top-center'
      //         });
      //         this.load();
      //         this.showTable = true;
      //         this.showRegister = false;
      //         this.$refs.dropzoneCreate.removeAllFiles();
      //       }
      //     }
      //     else{
      //       this.loadingForm = false;
      //       this.$toasted.error(response.data.message, {
      //         duration: 1500,
      //         position: 'top-center'
      //       });
      //       this.form.files = [];
      //       this.form.adminCode = '';
      //       this.form.nameAdmin = '';
      //       this.showTable = true;
      //       this.showRegister = false;
      //       this.$refs.dropzoneCreate.removeAllFiles();
      //     }
      //   })
      //   .catch( (error) =>{
      //     this.loadingForm = false;
      //     this.$toasted.error('Error', {
      //         duration: 1500,
      //         position: 'top-center'
      //     });
      //   });
      // }
      // //event.preventDefault()
    },
    onReset(event) {
      // event.preventDefault()
      // Reset our form values
      this.form.nameDoc = ''
      this.form.codeDoc = ''
      this.form.timeDoc = null
      this.form.dniDoc = '';
      this.form.adminCode = '';
      this.form.files = [];
      this.show = true,
      this.showTable = true,
      this.showRegister = false
      // this.$nextTick(() => {
      //   this.show = true,
      //   this.showTable = true,
      //   this.showRegister = false
      // })
    },
    setearTotal(){
      this.formOrden.totales = this.formOrden.subtotal - this.formOrden.descuento 
    },
    quitarItem(i){
      this.productoGeneral.splice(i,1)
    },
    AgregarItem(){
      this.loadingModal = true;
      if(this.formProducto.SKU == null || this.formProducto.SKU == "" ||
         this.formProducto.codFabrica == null || this.formProducto.codFabrica == "" ||
         this.formProducto.marca == null || this.formProducto.marca == "" ||
         this.formProducto.descripcion == null || this.formProducto.descripcion == "" ||
         this.formProducto.unidad == null || this.formProducto.unidad == "" ||
         this.formProducto.cantidad == null || this.formProducto.cantidad == "" ||
         this.formProducto.precioU == null || this.formProducto.precioU == "" ||
         this.formProducto.precioT == null || this.formProducto.precioT == "" 
         ){
          this.loadingModal = false;
          this.$toasted.error('Rellene todos los campos requeridos', {
          duration: 1000,
          position: 'top-center'
          });

      }else{
      this.productoGeneral.push(this.formProducto);
      this.formOrden.subtotal = this.formProducto.precioT;
      console.log('producto',this.productoGeneral);
      if(this.productoGeneral.length >= 1){
        this.loadingModal = false;
        this.$root.$emit('bv::hide::modal', 'modal-add-item')
        this.$toasted.success('Item Agregado Correctamente', {
           duration: 1500,
           position: 'top-center'
        });
      }else{
        this.loadingModal = false;
        this.$toasted.error('No se pudo agregar el item, intente de nuevo', {
           duration: 1500,
           position: 'top-center'
        });
      }
      }
      
      

    },
    obtenerNit(){
      this.nit = this.facturarList.filter(item => item.nombre == this.formOrden.facturar);
      this.formOrden.NIT= this.nit;
    },
    getAdministrado(){
      this.loadingDni = true;
      setTimeout(()=>{   
          this.$toasted.error('Error al buscar el SKU', {
          duration: 1500,
          position: 'top-center'
          });
      this.loadingDni = false;}, 300);
      // this.loadingDni = false;
      // this.loadingDni = true;
      // axios.get(config.hostname+'adminstrado/obtener/'+this.form.dniDoc)
      // .then( (response) =>{
      //   if(response.data.status === 'success'){
      //     this.loadingDni = false;
      //     var apat;
      //     var amat;
      //     if(response.data.data.c_appat_adm != null){
      //         apat = response.data.data.c_appat_adm + ' ';
      //     }else{
      //       apat = '';
      //     }
      //     if(response.data.data.c_apmat_adm != null){
      //         amat = response.data.data.c_apmat_adm + ' ';
      //     }else{
      //       amat = '';
      //     }
      //     this.form.nameAdmin = apat + amat + response.data.data.c_nomb_adm ;
      //     this.form.adminCode = response.data.data.id_administrado;
      //     this.$toasted.success(response.data.message, {
      //     duration: 1500,
      //     position: 'top-center'
      //     });
      //   }
      //   else{
      //     this.$toasted.error(response.data.message, {
      //     duration: 1500,
      //     position: 'top-center'
      //     });
      //     this.loadingDni = false;
      //     this.form.dniDoc = response.data.data.id_administrado;
      //   }
      // })
      // .catch( (error) =>{
      //   this.loadingDni = false;
      //   console.log(error);
      // });
    },
    parseDate(date) {
      const dateSet = date.toDateString().split(' ');
      return `${date.toLocaleString('en-us', { month: 'long' })} ${dateSet[2]}, ${dateSet[3]}`;
    },
    checkAll(ev, checkbox) {
      const checkboxArr = (new Array(this[checkbox].length)).fill(ev.target.checked);
      Vue.set(this, checkbox, checkboxArr);
    },
    changeCheck(ev, checkbox, id) {
      this[checkbox][id] = ev.target.checked;
      if (!ev.target.checked) {
        this[checkbox][0] = false;
      }
    },
    getRandomData() {
      const result = [];

      for (let i = 0; i <= 8; i += 1) {
        result.push(Math.floor(Math.random() * 20) + 1);
      }

      return [{data: result}];
    },
    getRandomColor() {
      const {primary, success, info, danger} = this.appConfig.colors;
      const colors = [info, primary, danger, success];
      return {colors: [colors[Math.floor(Math.random() * colors.length)]]}
    },
    loadTypeDoc(){
      axios.get(config.hostname+'maestro/obtener_tipo_doc_exp')
      .then( (response) =>{
        this.typeDoc = response.data.data;
        this.typeDoc.forEach(function (element) {
          element.text = element.c_nomb_tipo_doc_exp;
          element.value = element.id_tipo_doc_exp;
        });
      })
      .catch( (error) =>{
        console.log(error);
      });
    },
    guiaGet(){
      axios.get('https://almacenes-q4-default-rtdb.firebaseio.com/guia.json')
      .then((response) =>{
        this.guiaFiltered = response.data;
        console.log('solicitante',this.guiaFiltered);
        this.solicitantelist = this.guiaFiltered.filter(item => item.grupo_guia == "Area Solicitante");
        this.solicitantelist.forEach(function(element,index){
          element.text =  element.nombre
          element.value = element.nombre
        })
        this.facturarList = this.guiaFiltered.filter(item => item.grupo_guia == "Grupo Vuela");
        this.facturarList.forEach(function(element,index){
          element.text =  element.nombre
          element.value = element.nombre
        })
        this.pagoList  = this.guiaFiltered.filter(item => item.grupo_guia == "Tipo Pago")
        this.pagoList.forEach(function(element,index){
          element.text =  element.nombre
          element.value = element.nombre
        })
        this.monedaList  = this.guiaFiltered.filter(item => item.grupo_guia == "moneda")
        this.monedaList.forEach(function(element,index){
          element.text =  element.nombre
          element.value = element.nombre
        })
        this.entregaList  = this.guiaFiltered.filter(item => item.grupo_guia == "Tiempo de Entrega")
        this.entregaList.forEach(function(element,index){
          element.text =  element.nombre
          element.value = element.nombre
        })
        console.log('solicitante',this.solicitantelist);
        console.log('solicitante',this.facturarList);
      })
    },
    proveedorGet(){
      axios.get('https://almacenes-q4-default-rtdb.firebaseio.com/proveedor.json')
      .then((response) =>{
        this.proveedoresArr = response.data;
        this.proveedoresArr.forEach(function(element){
          element.text =  element.razon_social
          element.value = element.razon_social
        })
        
      } )
    },
    matches() {
        this.nit = this.facturarList.filter(item => item.nombre == this.formOrden.facturar);
        this.formOrden.NIT = this.nit[0].codigo_1
        console.log('facturar',this.formOrden.NIT);   
    },
    descripProducto() {
        if(this.formProducto.SKU == "1231-Wirel-Fuen-PO-unidad"){
          this.formProducto.codFabrica = "PA1024-3HU";
          this.formProducto.marca = "POWER TRON";
          this.formProducto.descripcion = "ADAPTADOR DE 24V - 1AMP";
          this.formProducto.precioU = 24.5;
          
          console.log('facturar',this.formProducto.SKU); 
        }else if(this.formProducto.SKU == "1232-Wirel-SAF-SA-unidad"){
          this.formProducto.codFabrica = "I0AB4811";
          this.formProducto.marca = "SAF";
          this.formProducto.descripcion = "Fuente de alimentacion cfip ac -ps  SAF";
          this.formProducto.precioU = 24.5;
        }
          
    },
    setearPreciot(){
      this.formProducto.precioT =  this.formProducto.precioU * this.formProducto.cantidad;
    },
    postOrden(){
      console.log('orden',this.formOrden);
      this.formOrden.producto = this.productoGeneral;
      if(this.formOrden.producto == null || this.formOrden.proveedor == null || this.formOrden.facturar == null ||
          this.formOrden.descripcion == null  || this.formOrden.fecha == null || this.formOrden.pago == null ||
          this.formOrden.moneda == null || this.formOrden.entrega == null){
            this.$toasted.error('Rellene todos los campos requeridos', {
            duration: 1000,
            position: 'top-center'
          });
          }else{
              axios.get('https://almacenes-q4-default-rtdb.firebaseio.com/orden de compra.json')
              .then( (response) =>{
                console.log('response',response);
                this.formOrden['id_orden']= response.data.length
                
                axios.put('https://almacenes-q4-default-rtdb.firebaseio.com/orden de compra/'+response.data.length+'.json',this.formOrden,
                {
                  headers: {
                    'Content-Type':'multipart/form-data; '
                  }
                })
                .then( (response) =>{
                  this.$router.push('/app/aprobarOrden');
                  this.$toasted.success('Se registró la orden de compra', {
                  duration: 1000,
                  position: 'top-center'
                });
                });
              })
            
            
          }
         
    }
  },
  computed: {
    
    
  },
  created() {
    this.usuario = window.localStorage.getItem('usuario');
    this.formOrden.usuario = this.usuario;
    this.proveedorGet();
    this.guiaGet();
    // this.loadTypeDoc();
    //this.user = JSON.parse(window.localStorage.getItem('user'));
    //this.id_user = this.user.id_usuario;
    //if (window.localStorage.getItem('authenticated') === 'true') {
     //axios.get(config.hostname+'expediente/obtener_expediente_area/1/'+this.page+'/'+this.perpage+'/'+this.id_user)
     // .then( (response) =>{
      //  this.expedientes = response.data.data;
       // this.allRows = Number(response.data.total);
       // this.loadingTable = false;
       // this.showTable = false;
     // })
     // .catch( (error) =>{
     //   console.log(error);
     // });
   // }
    //else{
     // this.$router.push('/login');
    //}
  }
};
</script>

<style src="./GenerarOrden.scss" lang="scss" scoped >
</style>

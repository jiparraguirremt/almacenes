<template>
  <div class="auth-page">
    <b-container>
      <h5 v-if="!showRegister" class="auth-logo">
        <!-- <i class="fa fa-circle text-primary"></i> -->
        <i class="fa fa-circle text-danger"></i>
        Iniciar sesión
        <i class="fa fa-circle text-danger"></i>
      </h5>
      <h5 v-if="showRegister" class="auth-logo">
        <i class="fa fa-circle text-primary"></i>
        Recuperar contraseña
        <i class="fa fa-circle text-primary"></i>
      </h5>
      <Widget v-if="!showRegister" class="widget-auth mx-auto" title="<h3 class='mt-0'>Inicie sesión en Vuela Almacenes</h3>" customHeader>
        <p class="widget-auth-info mt-2">
            Use su cuenta de Google para iniciar sesión.
        </p>
        <form class="mt" @submit.prevent="login">
          <b-alert class="alert-sm" variant="danger" :show="!!errorMessage">
            {{errorMessage}}
          </b-alert>
          <div class="form-group">
            <input class="form-control no-border" ref="email"  type="email" name="email" placeholder="Email" v-model="email"/>
          </div>
          <div class="form-group">
            <div class="row">
              <b-col cols="10" class="pr-0">
                <input class="form-control no-border input-pass" ref="password"  type="password" id="password"
                name="password" placeholder="Contraseña" v-model="password"/>
              </b-col>
              <b-col cols="2" class="pl-0 pr-0">
                <b-button @click="showHide()" class="btn-eye" variant="white">
                  <b-icon icon="eye-fill" v-if="!showPassword" aria-hidden="true"></b-icon>
                  <b-icon icon="eye-slash-fill" v-if="showPassword" aria-hidden="true"></b-icon>
                </b-button>
              </b-col>
            </div>
          </div>
          <b-button type="submit" size="sm" class="auth-btn mb-1" variant="inverse" v-if="!loading">Iniciar sesión</b-button>

            <a href="#" class="d-block text-right" @click="showRegister = true" v-if="!loading">Recuperar contraseña</a> 
        </form>
        <div class="data-loader">
          <i class="la la-spinner la-spin" v-if="loading"></i>
        </div>
        
      </Widget>

      <Widget v-if="showRegister" class="widget-auth mx-auto" title="<h3 class='mt-0'>Recuperar contraseña</h3>" customHeader>
        <p class="widget-auth-info">
            <br>
            Use su Email para recuperar su contraseña.
        </p>
        <form class="mt" @submit.prevent="rememberPassword">
          <b-alert class="alert-sm" variant="danger" :show="!!errorMessage">
            {{errorMessage}}
          </b-alert>
          <div class="form-group">
            <input class="form-control no-border" ref="email" required type="email" name="email" placeholder="Email" v-model="email"/>
          </div>
          <b-button type="submit" size="sm" class="auth-btn mb-1" variant="inverse" v-if="!loading">Recuperar contraseña</b-button>
          <a href="#" class="d-block text-left" @click="showRegister = false" v-if="!loading">Volver</a>
          <!-- <router-link class="d-block text-right" to="login" @click="showRegister = false">Recuperar contraseña</router-link> 
          <p class="widget-auth-info">O inicia sesión con</p>
          <div class="social-buttons">
            <b-button variant="primary" class="social-button mb-2">
              <i class="social-icon social-google"></i>
              <p class="social-text">GOOGLE</p>
            </b-button>
           <b-button variant="success" class="social-button">
              <i class="social-icon social-microsoft"></i>
              <p class="social-text">MICROSOFT</p>
            </b-button>
          </div>-->
        </form>
        <div class="data-loader">
          <i class="la la-spinner la-spin" v-if="loading"></i>
        </div>
        <!--<p class="widget-auth-info">
          ¿No tienes cuenta? Crea tu cuenta ahora!
        </p>
        <router-link class="d-block text-center" to="login">Crear una Cuenta</router-link>-->
      </Widget>
    </b-container>
    <footer class="auth-footer">
      2021 &reg; Registrado por <a href="https://vuela.bo/">Vuela</a>
    </footer>
  </div>
</template>

<script>
import Widget from '@/components/Widget/Widget';
import config from '../../config';
export default {
  name: 'LoginPage',
  components: { Widget },
  data() {
    return {
      ip:config.ip,
      email: '',
      password: '',
      loading:false,
      errorMessage: null,
      showRegister: false,
      showPassword: false,
      usuarios:null,
    };
  },
  methods: {
    showHide(){
      if(this.showPassword == true){
        this.showPassword = false;
        document.getElementById('password').setAttribute('type', 'password');
      }else{
        this.showPassword = true;
        document.getElementById('password').setAttribute('type', 'text');
      }
    },
    recuperar(){
      this.showRegister = true;
    },
    login() {
      this.loading = true;
      if(this.email == "" || this.password == ""){
        this.$toasted.error("Falta colocar email o contraseña", {
            
            duration: 1500,
            position: 'top-center'
        });
        this.loading = false;
        return;
      }
      axios.get('https://almacenes-q4-default-rtdb.firebaseio.com/usuario.json', {
      c_usuario: this.email,
      c_contrasena_usuario: this.password
      //c_usuario: 'drem.piura.sis@gmail.com',
      //c_contrasena_usuario: '1234'
      })
      .then( (response) =>{
        this.loading = false;
        this.usuarios = response.data;
        console.log('usuario',this.usuarios);
        this.usuarios.forEach(data => {
          if(data.correo === this.email && data.contrasena == this.password ){
            this.$toasted.success('Usuario y contraseña correctos', {
            duration: 1500,
            position: 'top-center'
            });
            setTimeout( ()=> {
              window.localStorage.setItem('usuario',this.email);
              this.$router.push('/app/generarOrden');
            }, 1000);
          }else{
          this.$toasted.error('Usuario y contraseña incorrectos', {
            duration: 1500,
            position: 'top-center'
          });
        }
          
        });
        
        
      })
      .catch( (error) =>{
        console.log('error',error);
      });
    },
    rememberPassword(){
      this.loading = true;
      axios.post(config.hostname+'user/restablecer_contrasena', {
      c_usuario: this.email,
      })
      .then( (response) =>{
        this.loading = false;
        this.email = '';
        this.password = '';
        if(response.data.status === 'success'){
          this.$toasted.success(response.data.message, {
            duration: 2000,
            position: 'top-center'
          });
          setTimeout( ()=> {
              this.showRegister = false;
          }, 500);
        }
        else{
          this.$toasted.error(response.data.message, {
            duration: 1500,
            position: 'top-center'
          });
        }
      })
      .catch( (error) =>{
        console.log(error);
      });
    }
  },
  created() {
    if (window.localStorage.getItem('authenticated') === 'true') {
      this.$router.push('/app/generarOrden');
    }
  },
};
</script>
<style src="./Loader.scss" lang="scss"/>
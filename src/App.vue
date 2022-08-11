<template>
  <div id="app" class="app">
    <div class="header">
      <h1>PAPAS FERSAN</h1>
      <h2>Papas y Yucas congeladas para freir</h2>
      <nav>
        <button v-on:click="loadProducto">Productos</button>
        <button v-if="!is_auth" v-on:click="loadLogIn">Entrar</button>
        <button v-if="is_auth" v-on:click="loadPedidos">Pedidos</button>
        <button v-if="is_auth" v-on:click="loadHome">Inicio</button>
        <button v-if="is_auth" v-on:click="logOut">Cerrar Sesion</button>
        <button v-if="!is_auth" v-on:click="loadSignUp">Registrarse</button>
        <!--
          <button v-if="is_auth">Inicio</button>
          <button v-if="is_auth">Cuenta</button>
          <button v-if="is_auth">Cerrar Sesion</button>
          <button v-if="is_auth" v-on:click="loadHome">Inicio</button>
        -->
      </nav>
    </div>
    <div class = "main-component">
      <router-view  
        v-on:completedLogIn="completedLogIn"
        v-on:completedSignUp="completedSignUp"
        v-on:logOut="logOut"
        >
        </router-view>
    </div>  
    <div class="footer">
      <h2>Ningun derecho reservado 2022</h2>
    </div>

  </div>
</template>

<script>

  export default {
    name: 'App',
    data: function(){
      return {
        is_auth:false
      }
    },

    components:{

    },

    methods:{
      verifyAuth: function(){
        this.is_auth = localStorage.getItem("isAuth") || false;
        if(this.is_auth==false)
          this.$router.push({name:'logIn'});
        else  
          this.$router.push({name:'home'});
      },
      loadHome: function(){
        this.$router.push({ name: "home"});
      },
      loadLogIn: function(){
        this.$router.push({name:'logIn'})
      },
      loadSignUp: function(){
        this.$router.push({name:'signUp'})
      },
      completedLogIn: function(data){
        localStorage.setItem("token_access",data.token_access);
        localStorage.setItem("token_refresh",data.token_refresh);
        localStorage.setItem("username",data.username);
        localStorage.setItem("isAuth", true);
        alert("Autenticacion exitosa");
        this.verifyAuth();
      },
      completedSignUp: function(data){
        alert("ReJistro Exitoso");
        this.completedLogIn(data);
      },
      logOut: function(){
        localStorage.clear();
        alert("Sesión cerrada");
        this.verifyAuth();
      },
      loadProducto: function (){
        this.$router.push({name:"producto"});
      },
      loadPedidos: function (){
        this.$router.push({name:"pedidos"});
      }
    },
    created: function(){
        this.verifyAuth()
    }
}
</script>

<style>
  body{
    margin: 0 0 0 0;
  }
  .header{
  margin: 1% 0% 0% 0%;
  padding: 100;
  width: 100%;
  height: 10vh;
  min-height: 100px;
  background-color: #f5b000 ;
  color:#2f3944 ;
  display: flex;
  justify-content: space-between;
  align-items: center;
  }
  .header h1 {
  width: 20%;
  text-align: center;
  }
  .header h2 {
  width: 30%;
  text-align: center;
  }
  .header nav {
  height: 100%;
  width: 20%;
  display: flex;
  justify-content: space-around;
  align-items: right;
  font-size: 20px;
  }
  .header nav button{
  height: 100%;
  color: #E5E7E9;
  background: #283747;
  border: 1px solid #E5E7E9;
  border-radius: 5px;
  padding: 10px 20px;
  }
  .header nav button:hover{
  color: #283747;
  background: #E5E7E9;
  border: 1px solid #E5E7E9;
  }
  .main-component{
  height: 65vh;
  margin: 0%;
  padding: 0%;
  background: #FDFEFE;
  }
  .footer{
  margin:0;
  padding:0;
  width: 100%;
  height: 10vh;
  min-height: 100px;
  background-color: #f5b000 ;
  color:#2f3944 ;
  }
  .footer h2{
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  }
</style>
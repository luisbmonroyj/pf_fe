<template>
    <div v-if="loaded" class="catalogo">
        <h1>Catalogo de Productos</h1>
        <!--
            <div>
            <form v-on:submit.prevent="getData()">
                <input type="number" v-model="producto.dato" placeholder="Codigo producto">
                <br>
                <button type="submit">Buscar</button>
            </form>
        </div>
            -->
        <table>
            <tr>
                <td class="anterior">
                    <button v-on:click="anterior">ANTERIOR</button>
                </td>
                <td>
                    <h2><span>Nombre: </span>{{nombre}}</h2>
                    <h2><span>Presentacion </span>{{presentacion}}</h2>   
                    <h2><span>Precio: </span>{{precio}} COP </h2>   
                </td>
                <td class="siguiente">
                    <button v-on:click="siguiente">SIGUIENTE</button>
                </td>
                     
            </tr>
            <tr>
                <td>
                    <p> Cantidad </p>
                </td>
                <td>
                    <input type="number" v-model="cantidad" placeholder="1">
                </td>
                <td class="agregarProd">
                    <button v-on:click="agregar">AGREGAR</button>
                </td>
            </tr>
        </table>
        <div class="Hacer pedido">
                    <button v-on:click="pedir">HACER PEDIDO</button>
        </div>
        <div>
            <!--
            <h2>Nombre: <span>{{nombre}}</span></h2>
            <h2>Presentacion <span>{{presentacion}}</span></h2>
            <h2>Precio: <span>{{precio}} COP </span></h2>
            -->
        </div>
    </div>
</template>

<script>
import jwt_decode from "jwt-decode";
import axios from 'axios';

let jasonPedido = new Map();
//var abriendo = '"productos_usuario": [{';
//abriendo += jasonPedido.keys().to
let productos = "";
let contador = 0;
let inicio = '{"productos_usuario" : [';
let abriendo = "{";
let otro = "},\n";
let productosMap = new Map();


export default{
    name: "producto",
    
    data: function(){
        //jasonPedido.set(1,4)
        console.log(jasonPedido.keys());
        console.log(jasonPedido.values());
        console.log(jasonPedido);
        return {
            nombre_producto: "",
            Presentacion: "",
            precio: 0,
            loaded: false, //linea 2
            id_producto: 1,
            cantidad:1
        }
    },
    
    methods:{
        confirmar: function(){
            return alert("intentando el pedido");
           /* axios.post(
                "https://pf-app-api.herokuapp.com/cart/",
                productos,
                {headers: {}}
            )
                .then((result) => {
                    alert ("Pedido creado")
                    /*let dataSignUp = {
                        username: this.user.username,
                        token_access: result.data.access,
                        token_refresh: result.data.refresh,
                    }
                    
                    this.$emit('completedSignUp', dataSignUp)
                })
                .catch((error) => {
                    console.log(error)
                    alert("ERROR: Fallo en el pedido.");
                });*/
        
        },
        pedir: async function(){
        //pedir: function(){
            if (localStorage.getItem("token_access") === null || localStorage.getItem("token_refresh")=== null){
                this.$emit('logOut');
                return;
            }
            await this.verifyToken();
            let token = localStorage.getItem("token_access");
            let userId = jwt_decode(token).id_usuario.toString();
            axios.get(`https://pf-app-api.herokuapp.com/user/${userId}/`, 
                {headers: {'Authorization': `Bearer ${token}`}})
            .then((result) => {
                productos+='}],"id_usuario":'+ userId + '}';
                //var obj = JSON.parse(productos);
                

                alert(productosMap);
                confirmar();

                this.loaded = true;

                /*
                this.name = result.data.name;
                this.email = result.data.email;
                this.balance = result.data.acount.balance;
                */
            })
            .catch(() => {
            //    this.$emit('logOut');
                alert("no pude crear el pedido");
            });

        },
        verifyToken: function(){
            return axios.post("https://bankbe-luis-app.herokuapp.com/refresh/",
            {refresh: localStorage.getItem("token_refresh")},
            {headers:{ }})
            .then((result) => {
                localStorage.setItem("token_access",result.data.access);
            })
            .catch(() => {
                this.$emit('logOut');
            });
        },
        agregar: function(){
            if(contador<1){
                productos += inicio+abriendo+this.id_producto+":"+this.cantidad;
                contador++;
            }
            else{
                productos+=otro+abriendo+this.id_producto+":"+this.cantidad;
                contador++;
            }

            productosMap.set(this.id_producto, this.cantidad);
            let text = "";
                
                productosMap.forEach (function(value, key) {
                text += key + ' = ' + value;
                })
                alert(text);

            //alert(productos);
            return;
        },
        anterior: function(){
            if (this.id_producto > 1)
                this.id_producto = this.id_producto-1;
            this.getData();
            return;
        },
        siguiente: function(){
                this.id_producto = this.id_producto+1;
            this.getData();
            return;
        },
        getData: function(){
            //let id_producto = this.id_producto;//producto.dato;
            /*
            alert('https://pf-app-api.herokuapp.com/producto/'+id_producto);
            if (localStorage.getItem("token_access") === null || localStorage.getItem("token_refresh")=== null){
                this.$emit('logOut');
                return;
            }
            await this.verifyToken();
            let token = localStorage.getItem("token_access");
            let userId = jwt_decode(token).user_id.toString();
            axios.get('https://bankbe-luis-app.herokuapp.com/user/1/', 
                {headers: {'Authorization': `Bearer ${token}`}})
            axios.get(`https://bankbe-luis-app.herokuapp.com/user/${userId}/`, 
                {headers: {'Authorization': `Bearer ${token}`}})
            axios.get('https://pf-app-api.herokuapp.com/producto/1/', 
            */
           
            axios.get(`https://pf-app-api.herokuapp.com/producto/${this.id_producto}/`, 
                {headers: {}})
            .then((result) => {
                this.nombre = result.data.nombre_producto;
                this.presentacion = result.data.presentacion;
                this.precio = result.data.precio;
                this.loaded = true;
            })
            .catch(() => {
                //alert("no pude leer el producto");
                this.id_producto=1;
                this.getData();
                //this.$emit('logOut');
            });

        },
        
    },
    created: async function(){
        this.getData();
    },
}
</script>
<style>
    .catalogo{
        margin: 5;
        padding: 0%;
        height: 100%;
        width: 100%;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
    }
    .catalogo h1 {
        font-size: 60px;
        color: #0f1316;
    }
    .catalogo h2 {
        font-size: 40px;
        color: #283747;
    }

    .catalogo span{
        color: crimson;
        font-weight: bold;
    }
    .catalogo button{
    height: 100%;
    color: #E5E7E9;
    background: #283747;
    border: 1px solid #E5E7E9;
    border-radius: 5px;
    padding: 10px 20px;
    }
    .catalogo button:hover{
    color: #283747;
    background: #E5E7E9;
    border: 1px solid #E5E7E9;
    }

    .agregarProd {
        justify-content: center;
    }
  
</style>
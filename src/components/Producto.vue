<template>
    <div v-if="loaded" class="catalogo">
        <h1>Catalogo de Productos</h1>
        <table>
           <tr>
                <td class="anterior">
                    <button v-on:click="anterior">ANTERIOR</button>
                </td>
                <td class="container_catalogo">
                    <h2><span>Nombre: </span>{{nombre}} </h2>
                    <h2><span>Presentacion </span>{{presentacion}} </h2>   
                    <h2><span>Precio: </span>{{precio}} COP </h2>   
                </td>
                <td class="siguiente">
                    <button v-on:click="siguiente">SIGUIENTE</button>
                </td>
            </tr>
            <tr>
                <td class="lblCantidad">
                    <p> Cantidad </p>
                </td>
                <td>
                    <div>
                        <input type="number" v-model="cantidad" placeholder="1"> 
                        <button v-on:click="agregar">AGREGAR</button>
                    </div>
                </td>
            </tr>
            <tr>
                <td>
                    <p></p>
                </td>
                <td>
                    <p></p>
                </td>
                <td>
                    <button v-on:click="pedir">HACER PEDIDO</button>
                </td>
            </tr>

        </table>
        <div class="hacerPedido">
            
        </div>
    </div>    
</template>

<script>
import jwt_decode from "jwt-decode";
import axios from 'axios';

let contador = 0;
let inicio = '{"productos_usuario" : [';
let abriendo = "{";
let otro = "},\n";
let productosMap = new Map();
let jsonCrudo = inicio+abriendo;
let otravariable;   
            

export default{
    name: "producto",
    data: function(){
        //jasonPedido.set(1,4)
        return {
            nombre_producto: "",
            Presentacion: "",
            precio: 0,
            loaded: false, //linea 2
            id_producto: 1,
            cantidad:1,
            id_usuario:null,
            productos_usuario:null
        }
    },
    
    methods:{
        pedir: async function(){
            //pedir: function(){
                let cont = 0;
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
                this.loaded = true;
                productosMap.forEach (function(value, key) {
                    if (cont<productosMap.size-1){
                        jsonCrudo += key + ' : ' + value+"},{";
                    }
                    else jsonCrudo += key + ' : ' + value+"}]";
                    cont++;
                })
                jsonCrudo+=',"id_usuario":'+ userId + '}';
                alert(jsonCrudo);
                console.log(jsonCrudo);
                otravariable = JSON.parse(jsonCrudo)
                console.log(otravariable);
                
                axios.post("https://pf-app-api.herokuapp.com/cart/",
                otravariable,
                {headers: {}})
                .then((result) => {
                   alert ("Pedido creado")

               })
                .catch(() => {
                //this.$emit('logOut');
                    alert("no pude crear el pedido");
                });
            })
            .catch(() => {
            //    this.$emit('logOut');
                alert("no pude crear el pedido");
            });
            confirmar(otravariable);
        },
        
        verifyToken: function(){
            return axios.post("https://pf-app-api.herokuapp.com/refresh/",
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
                        let strIdProd =   '"'+this.id_producto.toString()+'"';
            productosMap.set(strIdProd, this.cantidad);
            alert(" Producto agregado");
            this.cantidad = 1;
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

        anterior: function(){
            if (this.id_producto > 1)
                this.id_producto--;
            this.getData();
        },
        siguiente: function(){
                this.id_producto++;
            this.getData();
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
    .container_catalogo {
        border: 3px solid #283747;
        border-radius: 10px;
        width: 100%;
        height: 60%;
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
    .hacerPedido{
        padding: 400px 200px 300px auto;
    }
    .anterior{
        height: 100%;
    }
    .siguiente{
        height: 100%;
    }
    
    .agregarProd {
        justify-content: center;
    }
    .lblCantidad{
        align-items: right;   
        justify-content: right; 
    }
  
</style>
<template>
  <div>
    <h2>Lista de Productos</h2>
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>Nombre</th>
          <th>Descripción</th>
          <th>Precio</th>
          <th>Cantidad</th>
          <th>Almacén</th>
          <th>Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="producto in productos" :key="producto.id">
          <td>{{ producto.id }}</td>
          <td>{{ producto.nombre }}</td>
          <td>{{ producto.descripcion }}</td>
          <td>{{ producto.precio }}</td>
          <td>{{ producto.cantidad }}</td>
          <td>{{ producto.almacen.nombre }}</td>
          <td>
            <button @click="seleccionarProducto(producto)">Modificar</button
            >&nbsp;&nbsp;
            <button @click="eliminarProducto(producto.id)">Eliminar</button>
          </td>
        </tr>
      </tbody>
    </table>

    <h2>Agregar Producto</h2>
    <form @submit.prevent="agregarProducto">
      <label for="nombre">Nombre:</label>
      <input type="text" id="nombre" v-model="nuevoProducto.nombre" required />
      <br />
      <label for="descripcion">Descripción:</label>
      <textarea
        id="descripcion"
        v-model="nuevoProducto.descripcion"
        required
      ></textarea>
      <br />
      <label for="precio">Precio:</label>
      <input
        type="number"
        id="precio"
        v-model="nuevoProducto.precio"
        required
      />
      <br />
      <label for="cantidad">Cantidad:</label>
      <input
        type="number"
        id="cantidad"
        v-model="nuevoProducto.cantidad"
        required
      />
      <br />
      <label for="almacen">Almacén:</label>
      <select id="almacen" v-model="nuevoProducto.id_almacen" required>
        <option
          v-for="almacen in almacenes"
          :key="almacen.id"
          :value="almacen.id"
        >
          {{ almacen.nombre }}
        </option>
      </select>
      <br />
      <button type="submit">Agregar</button>
    </form>

    <form v-if="productoSeleccionado" @submit.prevent="modificarProducto">
      <h2>Modificar Producto</h2>
      <label for="nombre">Nombre:</label>
      <input
        type="text"
        id="nombre"
        v-model="productoSeleccionado.nombre"
        required
      />
      <br />
      <label for="descripcion">Descripción:</label>
      <textarea
        id="descripcion"
        v-model="productoSeleccionado.descripcion"
        required
      ></textarea>
      <br />
      <label for="precio">Precio:</label>
      <input
        type="number"
        id="precio"
        v-model="productoSeleccionado.precio"
        required
      />
      <br />
      <label for="cantidad">Cantidad:</label>
      <input
        type="number"
        id="cantidad"
        v-model="productoSeleccionado.cantidad"
        required
      />
      <br />
      <label for="almacen">Almacén:</label>
      <select id="almacen" v-model="productoSeleccionado.id_almacen" required>
        <option
          v-for="almacen in almacenes"
          :key="almacen.id"
          :value="almacen.id"
        >
          {{ almacen.nombre }}
        </option></select
      ><br />
      <button type="submit">Guardar Cambios</button>
    </form>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "VistaView",
  data() {
    return {
      productos: [],
      almacenes: [],
      nuevoProducto: {
        nombre: "",
        descripcion: "",
        precio: null,
        cantidad: null,
        id_almacen: null
      },
      productoSeleccionado: null
    };
  },
  mounted() {
    this.obtenerProductos();
    this.obtenerAlmacenes();
  },
  methods: {
    obtenerProductos() {
      axios
        .get("http://127.0.0.1:8000/api/productos")
        .then((response) => {
          this.productos = response.data.productos;
        })
        .catch((error) => {
          console.error("Hubo un error", error);
        });
    },

    obtenerAlmacenes() {
      axios
        .get("http://127.0.0.1:8000/api/almacenes")
        .then((response) => {
          this.almacenes = response.data.almacenes;
        })
        .catch((error) => {
          console.error("Hubo un error", error);
        });
    },
    agregarProducto() {
      axios
        .post("http://127.0.0.1:8000/api/productos/", this.nuevoProducto)
        .then((response) => {
          this.obtenerProductos();
          this.nuevoProducto = {
            nombre: "",
            descripcion: "",
            precio: null,
            cantidad: null,
            almacen: null
          };
          console.log(response);
        })
        .catch((error) => {
          console.error("Hubo un error al agregar el producto", error);
        });
    },

    seleccionarProducto(producto) {
      this.productoSeleccionado = { ...producto };
    },
    modificarProducto() {
      axios
        .put(
          `http://127.0.0.1:8000/api/productos/${this.productoSeleccionado.id}`,
          this.productoSeleccionado
        )
        .then((response) => {
          this.obtenerProductos();
          this.productoSeleccionado = null;
          console.log(response);
        })
        .catch((error) => {
          console.error("Hubo un error al modificar el producto", error);
        });
    },

    eliminarProducto(id) {
      axios
        .delete(`http://127.0.0.1:8000/api/productos/${id}`)
        .then((response) => {
          console.log(response);
          this.obtenerProductos();
        })
        .catch((error) => {
          console.error(error);
        });
    }
  }
};
</script>

<style>
table {
  border-collapse: collapse;
}
td,
th {
  border: 1px solid black;
  padding: 8px;
}

form {
  margin-bottom: 20px;
  padding: 20px;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

input[type="text"],
input[type="number"],
textarea,
select {
  width: 50%;
  padding: 8px;
  font-size: 18px;
  margin-bottom: 10px;
  border: 1px solid #000000;
  box-sizing: border-box;
}

button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  font-size: 18px;
}
</style>

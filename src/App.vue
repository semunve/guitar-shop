<script setup>
import { ref, reactive, onMounted, watch } from "vue";
import { db } from "./data/guitarras";
import Guitarra from "./components/Guitarra.vue";
import Header from "./components/Header.vue";
import Footer from "./components/Footer.vue";
const state = reactive({
  guitars: [db],
});
const guitarras = ref(db);
const carrito = ref([]);
const guitarra = ref({});

watch(
  carrito,
  () => {
    guardarLocalStorage();
  },
  {
    deep: true,
  }
);

//en onmounted se pueden usar reactive y ref
onMounted(() => {
  console.log("componente listo");
  guitarras.value = db;
  state.guitars = db;
  guitarra.value = db[3];
  const carritoStorage = localStorage.getItem("carrito");
  if (carritoStorage) {
    carrito.value = JSON.parse(carritoStorage);
  }
});

console.log(state.guitars); //se trata como un objeto
console.log(guitarras.value); //con ref se usa .value

const guardarLocalStorage = () => {
  localStorage.setItem("carrito", JSON.stringify(carrito.value));
};
const agregarCarrito = (guitarra) => {
  //console.log(guitarra);
  const existeCarrito = carrito.value.findIndex(
    (producto) => producto.id === guitarra.id
  );
  if (existeCarrito >= 0) {
    carrito.value[existeCarrito].cantidad++;
  } else {
    guitarra.cantidad = 1;
    carrito.value.push(guitarra);
  }
};

const decrementarCantidad = (id) => {
  console.log("menos", id);
  const index = carrito.value.findIndex((producto) => producto.id === id);
  if (carrito.value[index].cantidad <= 1) return;
  carrito.value[index].cantidad--;
};
const IncrementarCantidad = (id) => {
  const index = carrito.value.findIndex((producto) => producto.id === id);
  if (carrito.value[index].cantidad >= 5) return;
  carrito.value[index].cantidad++;
  console.log("mas", id);
};

const eliminarProducto = (id) => {
  console.log(id);
  carrito.value = carrito.value.filter((producto) => producto.id !== id);
};

const vaciarCarrito = () => {
  carrito.value = [];
};
</script>

<template>
  <Header
    :carrito="carrito"
    :guitarra="guitarra"
    @decrementar="decrementarCantidad"
    @Incrementar="IncrementarCantidad"
    @agregar-carrito="agregarCarrito"
    @eliminar-producto="eliminarProducto"
    @vaciar-carrito="vaciarCarrito"
  />
  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">
      <Guitarra
        v-for="guitarra in guitarras"
        v-bind:guitarra="guitarra"
        @agregar-Carrito="agregarCarrito"
      />

      <!-- FIN GUITARRA -->
    </div>
  </main>

  <Footer />
</template>

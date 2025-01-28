
  <script setup>
    import {ref, computed, onMounted, watch} from 'vue';
    import Header from './components/Header.vue';
    import Footer from './components/Footer.vue';
    import {db} from './data/guitarras';
    import Guitarra from './components/Guitarra.vue';

    const guitars = ref(db)
    const carrito = ref([])

    onMounted(() => {
      carrito.value = recuperaStorage()
    })

    function guardarLocalStorage(){
      localStorage.setItem('carrito', JSON.stringify(carrito.value))
    }
    function recuperaStorage(){
      const datosStrorage = localStorage.getItem('carrito')
      return datosStrorage ? JSON.parse(datosStrorage) : []
    }
    
    const total = computed(() => {
      let total =0
      carrito.value.forEach(guitar =>{total += guitar.cantidad * guitar.precio})
      return total
    })


    function agregarCarrito(guitar){
      const id = carrito.value.findIndex(g => g.id === guitar.id)
      if(id === -1){
        
      carrito.value.push({
        ...guitar,
        cantidad:1

      })
      console.log('agregando al carrito',guitar.nombre)
      }else{
        carrito.value[id].cantidad++
      }
    }
    function agergarUno(id){
      const idCarrito = carrito.value.findIndex(guitar => guitar.id === id)
      carrito.value[idCarrito].cantidad++
    }
    function disminuirUno(id){
      const idCarrito = carrito.value.findIndex(g => g.id === id)
      if(carrito.value[idCarrito].cantidad > 1){
        carrito.value[idCarrito].cantidad--
      }else{
        quitarGuitarra(id)
      }
    }
    function quitarGuitarra(id){
      carrito.value = carrito.value.filter(guitar => guitar.id !== id)
    }
    function vaciarCarrito(){
      carrito.value = []
    }


    watch(carrito, guardarLocalStorage, {deep:true})
  </script>
  <template>
    <Header
    :carrito="carrito"
    :total = "total"
    :guitarvai="guitars[3]"
    @agregar-carrito="agregarCarrito"
    @agrega-uno="agergarUno"
    @disminuir-uno="disminuirUno"
    @quitar-guitarra = "quitarGuitarra"
    @vaciar-carrito="vaciarCarrito"
    />
    

  <main class="container-xl mt-5">

      <h2 class="text-center">Nuestra Colección</h2>

      <div class="row mt-5">
        <Guitarra v-for="guitar in guitars"
        :key="guitar.id"
        :guitarra="guitar"
        @agregar-carrito="agregarCarrito"
        />    
      </div>
      



  </main>
  <Footer/>
</template>

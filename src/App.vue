<script setup>
  import { ref, reactive, onMounted, watch } from 'vue';
  import { uid } from 'uid'
  import Header from './components/Header.vue'
  import Formulario from './components/Formulario.vue'
  import Paciente from './components/Paciente.vue'
  

  const pacientes = ref([])

  const paciente = reactive({
    id: null,
    nombre:'',
    propietario:'',
    email:'',
    alta:'',
    sintomas:''
  })

  watch(pacientes, () =>{
        guardarLocalStorage()
    }, {
        deep:true
  })

  onMounted(() =>{
    const pacientesStorage = localStorage.getItem('pacientes')
    if(pacientesStorage){
      pacientes.value = JSON.parse(pacientesStorage)
    }
  })

  const guardarLocalStorage = () =>{
        localStorage.setItem('pacientes', JSON.stringify(pacientes.value))
    }

  const guardarPaciente = () => {
    if(paciente.id){
      const {id} = paciente
      const i = pacientes.value.findIndex(pacienteState => pacienteState.id === id )
      pacientes.value[i] = {...paciente}
    }else{
      pacientes.value.push({
        ...paciente,
        id:uid()
      })
    }
    
    Object.assign(paciente, {
      nombre:'',
      propietario:'',
      email:'',
      alta:'',
      sintomas:'',
      id:null
    })

  }

  const actualizarPaciente = (dato) => {
    Object.assign(paciente, {
      id:dato.id,
      nombre:dato.nombre,
      propietario:dato.propietario,
      email:dato.email,
      alta:dato.alta,
      sintomas:dato.sintomas
    })
  }

  const eliminarPaciente = (dato) =>{
    pacientes.value = pacientes.value.filter( paciente => paciente.id !== dato.id)
    //localStorage.removeItem('pacientes')
    //guardarLocalStorage()
  }

</script>

<template>
  <div class="container mx-auto mt-20 px-40">
    <Header />

    <div class="mt-12 md:flex">
      <Formulario 
        v-model:nombre="paciente.nombre"
        v-model:propietario="paciente.propietario"
        v-model:email="paciente.email"
        v-model:alta="paciente.alta"
        v-model:sintomas="paciente.sintomas"
        :id="paciente.id"
        @guardar-paciente="guardarPaciente"
        />

      <div class="md:w-1/2 md:h-screen overflow-y-scroll">
        <h3 class="font-black text-3xl text-center">Administra tus Pacientes</h3>

        <div v-if="pacientes.length > 0">
          <p class="text-lg mt-5 text-center mb-10">
            Información de
            <span class="text-indigo-600 font-bold">Pacientes</span>
          </p>
          <Paciente 
            v-for="paciente in pacientes" 
            :key="paciente.id"
            :paciente="paciente"
            @actualizar-paciente="actualizarPaciente"
            @eliminar-paciente="eliminarPaciente"
          />
        </div>
        <p v-else class="mt-10 text-2xl text-center">No Hay Pacientes</p>

      </div>

    </div>

  </div>
  
</template>

<style scoped>

</style>

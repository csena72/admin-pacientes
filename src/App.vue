<script setup>
import { ref, reactive, onMounted, watch } from 'vue'
import { uid } from 'uid'
import Header from './components/Header.vue'
import Formulario from './components/Formulario.vue'
import Paciente from './components/Paciente.vue'


const pacientes = ref([])

const paciente = reactive({
    id: null,
    nombre: '',
    propietario: '',
    email: '',
    alta: '',
    sintomas: '',
})

watch(pacientes, () => {
    guardarStorage()
}, { deep: true })

onMounted(() => {
    const pacientesLS = JSON.parse(localStorage.getItem('pacientes')) ?? []
    if (pacientesLS.length > 0) {
        pacientes.value = pacientesLS
    }
})

const guardarStorage = () => {
    localStorage.setItem('pacientes', JSON.stringify(pacientes.value))
}

const guardarPaciente = () => {

    if(paciente.id) {
        const index = pacientes.value.findIndex(pacienteState => pacienteState.id === paciente.id)

        pacientes.value[index] = {...paciente}

    } else {
        pacientes.value.push({
            ...paciente,
            id: uid(),
        })
    }

    //Limpiar formulario
    Object.assign(paciente, {
        nombre: '',
        propietario: '',
        email: '',
        alta: '',
        sintomas: '',
        id: null
    })
}

const actualizarPaciente = (id) => {
    const pacienteEditar = pacientes.value.filter(paciente => paciente.id === id) [0]
    Object.assign(paciente, pacienteEditar)
}

const eliminarPaciente = (id) => {
    const pacientesFiltrados = pacientes.value.filter(paciente => paciente.id !== id)
    pacientes.value = pacientesFiltrados
}

</script>

<template>
    <Header/>
    <div class="container mx-auto">
        <div class="mt-12 md:flex">
            <Formulario
                v-model:nombre="paciente.nombre"
                v-model:propietario="paciente.propietario"
                v-model:email="paciente.email"
                v-model:alta="paciente.alta"
                v-model:sintomas="paciente.sintomas"
                @guardar-paciente="guardarPaciente"
                :id="paciente.id"
            />

            <div class="md:w-1/2 md:h-screen overflow-y-scroll">
                <h3 class="font-black text-3xl text-center">Administra tus Pacientes</h3>

                <div v-if="pacientes.length > 0">
                    <p class="text-lg mt-5 text-center mb-10">
                        Información de <span class="text-indigo-600 font-bold">Pacientes</span>
                    </p>

                    <Paciente
                        v-for="paciente in pacientes"
                        :key="paciente.id"
                        :paciente="paciente"
                        @actualizar-paciente="actualizarPaciente"
                        @eliminar-paciente="eliminarPaciente"
                    />

                </div>
                <p v-else class="text-2xl mt-10 text-center">No hay Pacientes</p>
            </div>

        </div>
    </div>
</template>



<script setup>
import { ref, onMounted, nextTick } from 'vue'
import { confirmation, sendRequest } from '../../functions'
import { useAuthStore } from '../../stores/auth'
import axios from 'axios'
import Modal from '../../components/Modal.vue'
import Paginate from 'vuejs-paginate-next'
import { Bar, Pie } from 'vue-chartjs'
import {
    Chart as ChartJS, Title, Tooltip, Legend, BarElement, CategoryScale,
    LinearScale, ArcElement
} from 'chart.js';
ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale,
    ArcElement);

    import { useRoute } from 'vue-router';
    
const route = useRoute();

const authStore = useAuthStore()
axios.defaults.headers.common['Authorization'] = `Bearer ${authStore.authToken}`
const bloques = ref([]);
const promedios = ref([]);
const chartData = ref([]);
const chartOptions = ref([]);
const colors = ref([]);
const loaded = ref(false);
const random = () => {
    return Math.floor(Math.random() * 256);
}

//VARIABLE
const parametro = route.params.codigoFichaCalificacion;

onMounted(async () => {
    const info = await axios.get(`/api/fichacalificacion/bloquesficha/${parametro}`);
    info.data.map((row) => (
        bloques.value.push(row.bloque),
        promedios.value.push(row.promedio),
        colors.value.push("rgb(" + random() + ", " + random() + "," + random() + ")")
    ));
    chartOptions.value = { response: true }
    chartData.value = {
        labels: bloques.value,
        datasets: [
            { label: 'Promedio', data: promedios.value, backgroundColor: colors }
        ]
    };
    loaded.value = true;
})
</script>

<style scoped>
.contenedor-primario {
    margin-top: 60px;
    margin-left: 85px;
}
</style>

<template>
    <div class="row contenedor-primario d-flex justify-content-center align-items-center">
        <div class="col-md-9 ">
            <div class="row">
                <h2>Gráfica de promedios por bloques</h2>
                <hr>
                <br>
               
                    <Bar v-if="loaded" :data="chartData" :options="chartOptions"></Bar>
                
                

            </div>

        </div>

    </div>
</template>
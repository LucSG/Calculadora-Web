<template>
    <div class="container"> <!-- Container principal -->
        <div class="formulario-container"> <!-- Container do formulário -->
            <h1>Calcular Rescisão</h1>

            <form @submit.prevent="calcularRescisao">
                <div>
                    <label for="salario">Salário:</label>
                    <input type="number" id="salario" v-model.number="formData.salario" step="0.01" required>
                </div>

                <div>
                    <label for="tipoRescisao">Tipo de Rescisão:</label>
                    <select id="tipoRescisao" v-model="formData.tipoRescisao" required>
                        <option value="SEM_JUSTA_CAUSA">Sem Justa Causa</option>
                        <option value="POR_JUSTA_CAUSA">Com Justa Causa</option>
                        <option value="PEDIDO_DEMISSAO">Pedido de Demissão</option>
                        <option value="TERMINO_CONTRATO_DE_EXPERIENCIA">Termino de Contrato de Experiência</option>
                        <option value="RESCISAO_POR_ACORDO">Rescisão por Acordo</option>
                    </select>
                </div>

                <div>
                    <label for="dataAdmissao">Data de Admissão:</label>
                    <input type="date" id="dataAdmissao" v-model="formData.dataAdmissao" required>
                </div>

                <div>
                    <label for="dataDesligamento">Data de Desligamento:</label>
                    <input type="date" id="dataDesligamento" v-model="formData.dataDesligamento" required>
                </div>

                <div>
                    <label for="avisoPrevio">Aviso Prévio:</label>
                    <select id="avisoPrevio" v-model="formData.avisoPrevio" required>
                        <option :value="true">Indenizado</option>
                        <option :value="false">Trabalhado</option>
                    </select>
                </div>

                <div>
                    <label for="feriasVencidas">Tem Férias Vencidas?</label>
                    <select id="feriasVencidas" v-model="formData.feriasVencidas" required>
                        <option :value="true">Sim</option>
                        <option :value="false">Não</option>
                    </select>
                </div>

                <button type="submit">Calcular</button>
            </form>
        </div>
        <div v-if="carregando">
            <p>Carregando...</p>
        </div>
        <ResultadoRescisao :resultado="resultado" />

        <div v-if="erro">
            <p style="color: red;">Erro: {{ erro }}</p>
        </div>
    </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import axios from 'axios';
import ResultadoRescisao from './ResultadoRescisao.vue';

interface RescisaoForm {
    salario: number;
    tipoRescisao: string;
    dataAdmissao: string;
    dataDesligamento: string;
    avisoPrevio: boolean;
    feriasVencidas: boolean;
}

export default defineComponent({
    name: 'FormularioRescisao',
    components: {
        ResultadoRescisao,
    },
    setup() {
        const formData = ref<RescisaoForm>({
            salario: 0.00,
            tipoRescisao: 'SEM_JUSTA_CAUSA',
            dataAdmissao: '',
            dataDesligamento: '',
            avisoPrevio: false,
            feriasVencidas: false,
        });

        const resultado = ref<any>(null);
        const erro = ref<string | null>(null);
        const carregando = ref(false);

        const calcularRescisao = async () => {
            carregando.value = true;
            try {

                erro.value = null;
                const response = await axios.post('https://www.calculadorarescisao.site/rescisao/calcular', formData.value);
                resultado.value = response.data;
            } catch (error: any) {
                console.error("Erro ao calcular rescisão:", error);
                erro.value = error.message || 'Erro desconhecido';

                if (error.response && error.response.data) {
                    erro.value = JSON.stringify(error.response.data);
                }
            }
            finally {
                carregando.value = false;
            }
        };

        return {
            formData,
            resultado,
            erro,
            calcularRescisao,
            carregando,
        };
    },
});
</script>

<style scoped>
/* Estilos para o container principal */
.container {
    display: flex;
    flex-direction: column;
    align-items: center;
    /* Centraliza os itens na horizontal */
    width: 100%;
}

/* Estilos para centralizar o formulário */
.formulario-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    /* Centraliza horizontalmente */
    width: 100%;
    max-width: 600px;
    /* Largura máxima do container do formulário */
    margin: 0 auto;
    /* Centraliza o container do formulário horizontalmente */
    margin-bottom: 20px;
    /* Adiciona margem inferior para separar do resultado */
}

form {
    display: flex;
    flex-direction: column;
    width: 100%;
    /* Ocupa a largura total do container do formulário */
    max-width: 300px;
    margin-top: 20px;
}

form>div {
    margin-bottom: 10px;
}

label {
    display: block;
    margin-bottom: 5px;
}

input[type="number"],
input[type="date"],
select {
    width: 100%;
    padding: 8px;
    border: 1px solid #ccc;
    border-radius: 4px;
    box-sizing: border-box;
    /* Importante para que o padding não aumente o tamanho total do elemento */
}

button {
    padding: 10px 15px;
    background-color: #4CAF50;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
}

button:hover {
    background-color: #3e8e41;
}

pre {
    background-color: #f4f4f4;
    padding: 10px;
    border: 1px solid #ddd;
    overflow-x: auto;
}

.error {
    color: red;
}
</style>
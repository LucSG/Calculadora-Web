<template>
    <div class="resultado-page">
        <h1>Resultado do Cálculo da Rescisão</h1>

        <div v-if="resultado" class="resultado-container">
            <div class="coluna-proventos">
                <h2>Proventos</h2>
                <div class="resultado-item" v-for="(value, key) in resultado" :key="key">
                    <span class="resultado-label">
                        {{ typeof key === 'string' ? getLabel(key) : 'Chave Inválida' }}
                    </span>
                    <span class="resultado-value">{{ formatValue(value) }}</span>
                </div>
            </div>

            <div class="coluna-descontos">
                <h2>Descontos</h2>
                <div class="resultado-item" v-for="(value, key) in descontos" :key="key">
                    <span class="resultado-label">{{ getLabel(key) }}:</span>
                    <span class="resultado-value">{{ formatValue(value) }}</span>
                </div>
            </div>

            <div class="coluna-totais">
                <h2>Totais</h2>
                <div class="resultado-item">
                    <span class="resultado-label">{{ getLabel('totalBruto') }}:</span>
                    <span class="resultado-value">{{ formatValue(resultado.totalBruto) }}</span>
                </div>
                <div class="resultado-item">
                    <span class="resultado-label">{{ getLabel('totalLiquido') }}:</span>
                    <span class="resultado-value">{{ formatValue(resultado.totalLiquido) }}</span>
                </div>
            </div>
        </div>

        <div v-else>
            <p>Nenhum resultado disponível.</p>
        </div>
    </div>
</template>

<script lang="ts">
import { defineComponent, type PropType, computed } from 'vue';

export default defineComponent({
    name: 'ResultadoRescisao',
    props: {
        resultado: {
            type: Object as PropType<any>,
            required: false,
            default: null
        }
    },
    setup(props) {
        const proventos = computed(() => {
            if (!props.resultado) return {};
            const prov = { ...props.resultado }; // Copia para não modificar o original
            delete prov.inssSalario;
            delete prov.irrfSalario;
            delete prov.inss13;
            delete prov.irrf13;
            delete prov.totalBruto;
            delete prov.totalLiquido;
            return prov;
        });

        const descontos = computed(() => {
            if (!props.resultado) return {};
            return {
                inssSalario: props.resultado.inssSalario,
                irrfSalario: props.resultado.irrfSalario,
                inss13: props.resultado.inss13,
                irrf13: props.resultado.irrf13
            };
        });

        return {
            proventos,
            descontos
        };
    },
    methods: {
        getLabel(key: string): string {
            // Mapeamento de chaves para labels mais descritivos
            const labels: { [key: string]: string } = {
                saldoSalario: 'Saldo de Salário',
                inssSalario: 'INSS sobre Salário',
                irrfSalario: 'IRRF sobre IRRF Salário',
                decimoTerceiroProporcional: '13º Salário Proporcional',
                inss13: 'INSS sobre 13º',
                irrf13: 'IRRF sobre 13º',
                feriasVencidas: 'Férias Vencidas',
                umTercoFerias: '1/3 sobre Férias Vencidas',
                feriasProporcionais: 'Férias Proporcionais',
                umTercoFeriasProporcionais: '1/3 sobre Férias Proporcionais',
                avisoPrevioIndenizado: 'Aviso Prévio Indenizado',
                totalBruto: 'Total Bruto',
                totalLiquido: 'Total Líquido a Receber'

            };
            return labels[key] || key; // Retorna o label mapeado ou a chave original se não houver mapeamento
        },
        formatValue(value: any): string {
            if (typeof value === 'number') {
                return value.toLocaleString('pt-BR', {
                    style: 'currency',
                    currency: 'BRL'
                });
            } else if (typeof value === 'boolean') {
                return value ? 'Sim' : 'Não';
            } else {
                return value ? value.toString() : '-';
            }
        }
    },
});
</script>

<style scoped>
/*Sim eu sou pessimo com CSS, mas eu tento fazer o melhor que posso*/
.resultado-page {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 100%;
}

.resultado-container {
    display: flex;
    width: 100%;
    max-width: 1400px;
    justify-content: space-around;
}

.coluna-proventos,
.coluna-descontos,
.coluna-totais {
    width: 30%;
    display: flex;
    flex-direction: column;
    align-items: center;
    /* Centraliza os itens verticalmente */
}

.coluna-proventos h2,
.coluna-descontos h2,
.coluna-totais h2 {
    text-align: center;
    /* Centraliza o texto dos títulos */
}

.resultado-item {
    display: flex;
    width: 100%;
    margin-bottom: 5px;
    align-items: baseline;
    white-space: nowrap;
}

.resultado-label {
    font-weight: bold;
    width: 250px;
    text-align: right;
    margin-right: 10px;
}

.resultado-value {
    flex-grow: 1;
    text-align: left;
}

/* Estilo adicional para alinhar o valor à direita */
.coluna-totais .resultado-value {
    text-align: right;
}
</style>
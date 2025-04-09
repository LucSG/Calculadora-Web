<template>
    <div class="resultado-page">
        <h1>Resultado do Cálculo da Rescisão</h1>

        <div v-if="resultado" class="resultado-container">
            <div class="coluna-proventos">
                <h2>Proventos</h2>
                <div class="resultado-item" v-for="(value, key) in proventos" :key="key">
                    <span class="resultado-label">{{ getLabel(key) }}:</span>
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
                    <span class="resultado-label">Total de Descontos:</span>
                    <span class="resultado-value">{{ formatValue(totalDescontos) }}</span>
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
import { defineComponent, PropType, computed } from 'vue';

interface RescisaoResultado {
    saldoSalario: number;
    inssSalario: number;
    irrfSalario: number;
    decimoTerceiroProporcional: number;
    inss13: number;
    irrf13: number;
    feriasVencidas: number;
    umTercoFerias: number;
    feriasProporcionais: number;
    umTercoFeriasProporcionais: number;
    avisoPrevioIndenizado: number;
    totalBruto: number;
    totalLiquido: number;
}

interface RescisaoResultado {
    saldoSalario: number;
    inssSalario: number;
    irrfSalario: number;
    decimoTerceiroProporcional: number;
    inss13: number;
    irrf13: number;
    feriasVencidas: number;
    umTercoFerias: number;
    feriasProporcionais: number;
    umTercoFeriasProporcionais: number;
    avisoPrevioIndenizado: number;
    totalBruto: number;
    totalLiquido: number;
}

export default defineComponent({
    name: 'ResultadoRescisao',
    props: {
        resultado: {
            type: Object as PropType<RescisaoResultado>,
            required: false,
            default: null
        }
    },
    setup(props) {
        const proventos = computed(() => {
            if (!props.resultado) return {};
            const { inssSalario, irrfSalario, inss13, irrf13, totalBruto, totalLiquido, ...prov } = props.resultado;
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

        const totalDescontos = computed(() => {
            if (!props.resultado) return 0;
            return (
                (props.resultado.inssSalario || 0) +
                (props.resultado.irrfSalario || 0) +
                (props.resultado.inss13 || 0) +
                (props.resultado.irrf13 || 0)
            );
        });

        return {
            proventos,
            descontos,
            totalDescontos
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
            return labels[key] || key;
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
/* Estilos */
</style>

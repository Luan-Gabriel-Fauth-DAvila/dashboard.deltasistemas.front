<template>
    <div :style="{ maxHeight: '28vh' }">
    <button @click="valorVendasPorAgrupamento">teste</button>
        <canvas id="total_vendas_por_agrupamento_chart"></canvas>
    </div>
</template>

<script>
export default {
    name: 'circleGraph',
    props: {
        hostBack: String,
        urlSearch: String,
        filter: String,
        colors: Array,
    },
    methods: {
        async valorVendasPorAgrupamento () {
            const req = await fetch(this.hostBack+this.urlSearch+this.filter)
            const res = await req.json()
        
            const data = {
                labels: res.dscagrupamento,
                datasets: [{
                    label: 'Valor de Vendas Mensais',
                    backgroundColor: this.colors,
                    hoverOffset: 4,
                    data: res.total_vendas,
                }]
            };
            const config = {
                type: 'pie',
                data: data,
                options: {
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            display: true,
                            position: 'right'
                        },
                    },
                }
            };
            var chartStatus = Chart.getChart("total_vendas_por_agrupamento_chart");
            if (chartStatus != undefined) {
                chartStatus.data.datasets.data = res.total_vendas
                chartStatus.update()
            }else {
                const total_por_agrupamento_mensal = new Chart(
                    document.getElementById('total_vendas_por_agrupamento_chart'),
                    config
                );
            }
        },
    }
}
</script>
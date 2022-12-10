<template>
    <div id="total_vendas_mensal">
        <span></span>
        <div>
            <h2>Vendas por Mês</h2>
            <label>Total evolução <span class="material-icons-outlined" id="total_evolucao_arrow"></span><p id="total_evolucao"></p></label>
        </div>
        <div :style="{ maxHeight: '28vh' }">
            <div id="total_vendas_mensal_chart"></div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'lineGraph',
    props: {
        url: {
            type: String,
            
        }
    },
    methods: {
        async valorVendasMensais () {
            const req = await fetch(this.hostBack+'/total_vendas_mensal/'+this.defFilter())
            const res = await req.json()

            const total_vendas_mensal_chart = Highcharts.chart('total_vendas_mensal_chart', {
                plotOptions: {
                    series: {
                        // general options for all series
                    },
                    spline: {
                        color: 'rgb(67, 24, 255)',
                        colorIndex: 'rgb(134, 140, 255)',
                    },
                },
                chart: {
                    height: this.heightDefined
                },
                title: {
                    text: undefined,
                },
                xAxis: {
                    categories: res.dscmes,
                },
                yAxis: {
                    title: {
                        text: undefined,
                    }
                },
                series: [{
                    type: 'spline',
                    name: 'Meses',
                    data: res.total_vendas
                }]
            })
            let total = res.total_vendas.length
            if (parseInt(res.total_vendas[total-1]) < parseInt(res.total_vendas[total-2])) {
                self.total_evolucao_arrow.innerHTML = 'arrow_drop_down'
                self.total_evolucao_arrow.style = 'color: red;'
                self.total_evolucao.style = 'color: red;'
                self.total_evolucao.innerHTML = 100 - (res.total_vendas[total-1]/res.total_vendas[total-2]*100).toFixed(2) + '%';                
            }else{
                self.total_evolucao_arrow.innerHTML = 'arrow_drop_up'
                self.total_evolucao_arrow.style = 'color: green;'
                self.total_evolucao.style = 'color: green;'
                self.total_evolucao.innerHTML = (res.total_vendas[total-1]/res.total_vendas[total-2]*100).toFixed(2) - 100 + '%';                

            }
        },
    }
}
</script>

<style scoped>

</style>
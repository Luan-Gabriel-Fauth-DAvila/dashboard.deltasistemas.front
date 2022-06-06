<template>
    <div :style="{ height: '28vh' }" style="border: 1px solid black;">
        <div id="container"></div>
    </div>
</template>

<script>
export default {
    nome: 'Teste',
    head() {
        return {
            script: [
                {src: "https://code.highcharts.com/highcharts.js"},
                {src: "https://code.highcharts.com/modules/accessibility.js"},
            ]
        }
    },   
    data () {
        return {
            hostBackEnd: 'http://127.0.0.1:8000',
        }
    },
    methods: {
        async word () {
            const req = await fetch(this.hostBackEnd+'/total_vendas_mensal')
            const res = await req.json()

            let chart = Highcharts.chart('container', {
                plotOptions: {
                    series: {
                        // general options for all series
                    },
                    line: {
                        // shared options for all line series
                    }
                },
                title: {
                    text: 'Fruit Consumption'
                },
                xAxis: {
                    categories: res.desmes
                },
                yAxis: {
                    title: {
                        text: 'Fruit eaten'
                    }
                },
                series: [{
                    name: 'Jane',
                    data: res.total_vendas
                },]
            });
        },
    },
    mounted () {
        this.word()
        setInterval(() => {                
            this.word()
        }, 10000)
    }
}
</script>

<style>

</style>
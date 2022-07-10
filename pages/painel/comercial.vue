<template>
    <div>
        <Navbar />
        <section id="painel">
            <div id="filter">
                <div>
                    <label for="">Data Inicio</label>
                    <input type="date" id="data_ini">
                </div>
                <div>
                    <label for="">Data Fim</label>
                    <input type="date" id="data_fim">
                </div>
                <div>
                    <label for="">Agrupamento</label>
                    <select id="agrupamento">
                        <option v-for="agr in agrupamentos" :key="agr.codagrupamento" :value="agr.codagrupamento">{{ agr.dscagrupamento }}</option>
                    </select>
                </div>
                <div>
                    <button @click="defData" id="buttom_filter">Aplicar</button>
                </div>
            </div>
            <div id="total_vendas">
                <div>
                    <label>total de vendas</label>
                    <h4 id='total_vendas_value'>Sem dados</h4>
                </div>
                <span class="material-icons-outlined">trending_up</span>
            </div>
            <div id="total_cmv">
                <div>
                    <label>CMV</label>
                    <h4 id="total_cmv_value">Sem dados</h4>
                </div>
                <span class="material-icons-outlined">leaderboard</span>
            </div>
            <div id="total_lucro_bruto">
                <span class="material-icons-outlined">assessment</span>
                <div>
                    <label>Lucro bruto</label>
                    <h4 id="total_lucro_bruto_value">Sem dados</h4>
                </div>
            </div>
            <div id="valor_em_locacao">
                <span class="material-icons-outlined">payments</span>
                <div>
                    <label>Valor em locação</label>
                    <h4>Sem dados</h4>
                </div>
            </div>
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
            <div id="total_vendas_por_agrupamento">
                <div>
                    <h2>Vendas por Agrupamento</h2>
                    <label for="">Por Mês</label>
                </div>
                <div :style="{ maxHeight: '28vh' }">
                    <canvas id="total_vendas_por_agrupamento_chart"></canvas>
                </div>
            </div>
            <div id="meta_de_vendas">
                <div>
                    <h2>Meta de Vendas</h2><label>Por Mês</label>
                </div>
                <div>   
                    <div id="container_principal">
                        <b id="barra_cliente" v-if="metadevendas_total" >{{ metadevendas_atual }} de {{ metadevendas_total }}</b>
                        <b id="barra_cliente"  v-else >Sem dados</b>
                        <div id='barra_meta_vendas'>
                            <div id="barra_porcentagem" :style="{ backgroundColor: colors[2], width: tamanhoBarra(metadevendas_atual, metadevendas_total) }">
                                <b id="barra_valor" v-if="metadevendas_total">{{ tamanhoBarra(metadevendas_atual, metadevendas_total) }} %</b>
                                <b id="barra_valor" v-else>Sem Dados</b>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div id="ranking_de_vendas_por_vendedor">
                <div>
                    <h2>Ranking de Vendas Por vendedor<label style="font-weight: normal;">Por Mês</label></h2>
                </div>
                <div :style="{ maxHeight: '28vh' }">
                    <!-- <div id="container_principal" v-for="(r, index) in rankingvendedores_var" :key="index">
                        <div id='barra_total'>
                            <div id="barra_porcentagem" :style="{ backgroundColor: colors[index] ,width: tamanhoBarra(r.valor, rankingvendedores_sum) + '%' }">
                                <b id="barra_valor">{{ moneyFilter(r.valor) }}</b>
                            </div>
                        </div>
                        <h6 id="barra_cliente">{{ r.nome }}</h6>
                    </div> -->
                    <div id="ranking_de_vendas_por_vendedor_chart"></div>
                </div>
            </div>
            <div id="ranking_de_vendas_por_cliente">
                <div>
                    <h2>Ranking de Vendas Por Cliente<label style="font-weight: normal;">Por Mês</label></h2>
                </div>
                <div :style="{ maxHeight: '28vh' }">
                    <!-- <div id="container_principal" v-for="(r, index) in rankingclientes_var" :key="index">
                        <div id='barra_total'>
                            <div id="barra_porcentagem" :style="{ backgroundColor: colors[index] ,width: tamanhoBarra(r.total_vendas, rankingclientes_sum) + '%' }">
                                <b id="barra_valor">{{ moneyFilter(r.total_vendas) }}</b>
                            </div>
                        </div>
                        <h6 id="barra_cliente">{{ r.nome }}</h6>
                    </div> -->
                    <div id="ranking_de_vendas_por_cliente_chart"></div>
                </div>
            </div>
            <div id="ranking_produtos_mais_vendidos">
                <div>
                    <h2>Vendas Por Produto</h2>
                    <label for="">Mais Vendidos</label>
                </div>
                <div :style="{ maxHeight: '28vh' }">
                    <canvas id="produtos_mais_vendidos_chart"></canvas>
                </div>
            </div>
        </section>
    </div>
</template>

<script>
import Navbar from '/components/navbar.vue';

export default {
    name: 'Comercial',
    components: {
        Navbar,
    },
    head() {
        return {
            script: [
                {src: 'https://cdn.jsdelivr.net/npm/chart.js'},
                {src: "https://code.highcharts.com/highcharts.js",body: true},
                {src: "https://code.highcharts.com/modules/accessibility.js",body: true},
            ]
        }
    },              
    data () {
        return {
            hostBack: 'http://188.166.65.228:8000',
            hostFront: 'http://188.166.65.228:3000',
            rankingclientes_var: null,
            rankingclientes_total_vendas: null,
            rankingclientes_sum: 0,
            rankingvendedores_var: null,
            rankingvendedores_total_vendas: null,
            rankingvendedores_sum: 0,
            colors: [
                '#c438ef',
                '#ff409a',
                '#6452ff',
                '#05cd99',
                '#6452ff',
                '#ffc086',
                '#4318ff',
                '#05cd99',
            ],
            metadevendas_total: null,
            metadevendas_atual: null,
            heightDefined: 0,

            agrupamentos: null,
            class_nav: 'deactive',
            url: ''
        }
    },
    props: {
    },
    methods: {
        heightDefine () {
            if (window.screen.width < 630) {
                this.heightDefined = parseInt(window.screen.height)*0.30
            }else {
                this.heightDefined = parseInt(window.screen.height)*0.24
            }
        },
        async verifyLogin () {
            let data = {
                "token": sessionStorage.getItem("access")
            }
            const req = await fetch(this.hostBack+'/jwt/verify/', {
                method: 'POST',
                body: JSON.stringify(data),
                headers: {"Content-type": "application/json"}
            })
            const res = await req.json()


            if (req.status == '200') {
                let data = {
                    "refresh": sessionStorage.getItem("refresh")
                }
                const req_refresh = await fetch(this.hostBack+'/jwt/refresh/', {
                    method: 'POST',
                    body: JSON.stringify(data),
                    headers: {"Content-type": "application/json"}
                })
                const res_refresh = await req_refresh.json()
                sessionStorage.setItem('access', res_refresh.access)
            }else{
                window.location.href = this.hostFront+"/login/"
            }
        },
        defData () {
            const ini = self.data_ini.value.split('-', 3)
            const fim = self.data_fim.value.split('-', 3)
            window.location.href = (this.hostFront+'/painel/comercial/?data_ini='+ini[2]+'.'+ini[1]+'.'+ini[0]+'&data_fim='+fim[2]+'.'+fim[1]+'.'+fim[0])
        },
        defFilter () {
            const urlParams = new URLSearchParams(window.location.search);
            const data_ini = urlParams.get('data_ini')
            const data_fim = urlParams.get('data_fim')
            const urlFilter = '?data_ini='+data_ini+'&data_fim='+data_fim
            return urlFilter
        },
        async getAgrupamentos () {
            const req = await fetch(this.hostBack+'/agrupamentos/')
            const res = await req.json()
            
            this.agrupamentos = res
        },
        navBar () {
            if (this.class_nav == 'deactive') {
                this.class_nav = 'active'
            } else {                
                this.class_nav = 'deactive';
            }
        },
        moneyFilter (msg){
            return parseFloat(msg).toLocaleString('pt-BR', { style: 'currency', currency: 'BRL' })
        },
        tamanhoBarra (barra,total){
            const tamanho = parseFloat(parseInt(parseFloat(barra)/parseFloat(total)*100))
            if (tamanho < 10) {return 10}
            return tamanho;
        },
        corAleatoria (cores) {
            const num = Math.floor(Math.random() * cores.length)
            return (cores[num])
        },
        async valorVendas () {
            const req = await fetch(this.hostBack+'/total_vendas/'+this.defFilter())
            const res = await req.json()
            self.total_vendas_value.innerHTML = this.moneyFilter(res.total_vendas)
        },

        async valorCMV () {
            const req = await fetch(this.hostBack+'/total_cmv/'+this.defFilter())
            const res = await req.json()
            self.total_cmv_value.innerHTML = this.moneyFilter(res.total_cmv)
        },

        async valorLucroBrutoMensal () {
            const req = await fetch(this.hostBack+'/lucro_bruto_mensal/'+this.defFilter())
            const res = await req.json()
            self.total_lucro_bruto_value.innerHTML = this.moneyFilter(res.valor_total)
        },
        
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

        async valorVendasPorAgrupamento () {
            const req = await fetch(this.hostBack+'/vendas_por_agrupamento_mensal/'+this.defFilter())
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

        async metaDeVendas () {
            const req = await fetch(this.hostBack+'/meta_de_vendas')
            const res = await req.json()

            this.metadevendas_total = parseFloat(res.total_vendido);
            this.metadevendas_atual = parseFloat(res.valor);
        },

        async rankingVendasPorVendedor () {
            const req = await fetch(this.hostBack+'/total_de_vendas_por_vendedor')
            const res = await req.json()

            // this.rankingvendedores_var = res
            // this.rankingvendedores_sum = parseFloat(res[0].valor)
            // this.rankingvendedores_total_vendas = parseFloat(res.valor).toLocaleString('pt-BR', { style: 'currency', currency: 'BRL' })
            const total_vendas_mensal_chart = Highcharts.chart('ranking_de_vendas_por_vendedor_chart', {
                plotOptions: {
                    series: {
                        // general options for all series
                    },
                    bar: {
                        color: 'rgb(67, 24, 255)',
                        colorIndex: 'rgb(134, 140, 255)',
                    },
                },
                chart: {
                    height: parseInt(window.screen.height)*0.42
                },
                title: {
                    text: undefined,
                },
                xAxis: {
                    categories: res.nome
                },
                yAxis: {
                    title: {
                        text: undefined,
                    }
                },
                series: [{
                    type: 'bar',
                    name: 'Vendedores',
                    data: res.valor,
                },]
            })

        },

        async rankingVendasPorCliente () {
            const req = await fetch(this.hostBack+'/ranking_de_vendas_por_cliente')
            const res = await req.json()

            // this.rankingclientes_var = res
            // this.rankingclientes_sum = parseFloat(res[0].total_vendas)
            // this.rankingclientes_total_vendas = parseFloat(res.total_vendas).toLocaleString('pt-BR', { style: 'currency', currency: 'BRL' })

            const total_vendas_mensal_chart = Highcharts.chart('ranking_de_vendas_por_cliente_chart', {
                plotOptions: {
                    series: {
                        // general options for all series
                    },
                    bar: {
                        color: 'rgb(67, 24, 255)',
                        colorIndex: 'rgb(134, 140, 255)',
                    },
                },
                chart: {
                    height: parseInt(window.screen.height)*0.55
                },
                title: {
                    text: undefined,
                },
                xAxis: {
                    categories: res.nome
                },
                yAxis: {
                    title: {
                        text: undefined,
                    }
                },
                series: [{
                    type: 'bar',
                    name: 'Clientes',
                    data: res.total_vendas,
                },]
            })
        },
        
        async produtosMaisVendidos () {
            const req = await fetch(this.hostBack+'/ranking_de_vendas_por_produto')
            const res = await req.json()
            
            const data = {
                labels: res.nome,
                datasets: [{
                    label: 'Valor de Vendas Mensais',
                    backgroundColor: this.colors,
                    hoverOffset: 4,
                    data: res.qtd,
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
            var chartStatus = Chart.getChart("produtos_mais_vendidos_chart");
            if (chartStatus != undefined) {
                chartStatus.data.datasets.data = res.total_vendas
                chartStatus.update()
            }else {
                const total_por_agrupamento = new Chart(
                    document.getElementById('produtos_mais_vendidos_chart'),
                    config
                );
            }
        },

        
    },
    created () {
    },
    mounted () {
        this.getAgrupamentos()
        this.heightDefine()
        setInterval(() => {
            this.heightDefine()
        }, 1000)
        this.valorVendas()
        this.valorCMV()
        this.valorLucroBrutoMensal()
        this.valorVendasMensais()
        this.metaDeVendas()
        this.rankingVendasPorVendedor()
        this.valorVendasPorAgrupamento()
        this.rankingVendasPorCliente()
        this.produtosMaisVendidos()
        
        this.verifyLogin()
        setInterval(() => {
            this.valorVendas()
            this.valorCMV()
            this.valorLucroBrutoMensal()
            this.valorVendasMensais()
            this.metaDeVendas()
            this.rankingVendasPorVendedor()
            this.valorVendasPorAgrupamento()
            this.rankingVendasPorCliente()
            this.produtosMaisVendidos()
            
            this.verifyLogin()
        }, 30000)
    }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300&display=swap');
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-size: 2vh;
  font-family: 'Poppins', sans-serif;
}
html,body {
  background-color: #eeeeee;
}
small {
    line-height: 2.5vh;
    font-size: 1vh;
}
label {
    margin: unset;
    padding: unset;
    line-height: 2.5vh;
    font-size: 1.3vh;

    display: flex;
    align-items: center;
}
label span {
    line-height: 2.5vh;
    font-size: 2vh;
    color: rgb(0, 200, 0);
}
label p {
    line-height: 2.5vh;
    font-size: 1.3vh;
    color: rgb(0, 200, 0);
}
h2 {
    font-size: 3vh;
    line-height: 3vh;
}
h4 {
    line-height: 2.5vh;
}
h6 {
    margin-left: 1vh;
    font-size: 1.5vh;
    font-weight: bold;
}
#painel {
    display: grid;
    grid-template-columns: 25% 25% 25% 25%;
    grid-template-rows: 15vh 15vh 45vh 25vh 40vh 40vh;
    grid-template-areas:    "filter filter filter filter"
                            "total_vendas total_cmv total_lucro_bruto valor_em_locacao"
                            "total_vendas_mensal total_vendas_mensal total_vendas_por_agrupamento total_vendas_por_agrupamento"
                            "meta_de_vendas meta_de_vendas ranking_de_vendas_por_vendedor ranking_de_vendas_por_vendedor"
                            "ranking_de_vendas_por_cliente ranking_de_vendas_por_cliente ranking_de_vendas_por_vendedor ranking_de_vendas_por_vendedor"
                            "ranking_de_vendas_por_cliente ranking_de_vendas_por_cliente ranking_produtos_mais_vendidos ranking_produtos_mais_vendidos";
    margin: 5vh 10%;  
    padding: 0 0; 
    border-radius: 2vh;
}
@media only screen and (max-width: 630px) {
  #painel {
        display: grid;
        grid-template-columns: 50% 50%;
        grid-template-rows: 15vh 15vh 15vh 15vh 40vh 40vh 25vh 80vh 80vh 40vh;
        grid-template-areas:    "filter filter"
                                "filter filter"
                                "total_vendas total_cmv" 
                                "total_lucro_bruto valor_em_locacao"
                                "total_vendas_mensal total_vendas_mensal"
                                "total_vendas_por_agrupamento total_vendas_por_agrupamento"
                                "meta_de_vendas meta_de_vendas"
                                "ranking_de_vendas_por_vendedor ranking_de_vendas_por_vendedor"
                                "ranking_de_vendas_por_cliente ranking_de_vendas_por_cliente"
                                "ranking_produtos_mais_vendidos ranking_produtos_mais_vendidos";
        margin: 0;  
        padding: 5% 0;
   
    }
}
#filter {
    grid-area: filter;

    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;  
    align-items: center;

    background: linear-gradient(135deg, rgb(67, 24, 255), rgb(134, 140, 255));
    padding: 2vh 2vh;
    margin: 2vh;
    border-radius: .5vh;
    color: #fff;
    z-index: 0;
}
@media only screen and (max-width: 630px) {
    #filter {
        flex-direction: column;
    }
}
#filter #data_ini,
#filter #data_fim {
    border: unset;
    border-radius: 1vh;
    padding: 1vh;
}
#buttom_filter {
    border: unset;
    padding: 1vh;
    background-color: #fff;
    color: #1B2559;
    border-radius: .5vh;
}
#buttom_filter:hover {
    box-shadow: 0 0 5px 0;
    background-color: rgb(214, 214, 214);
}
#total_vendas {
    grid-area: total_vendas;

    display: flex;
    justify-content: space-evenly;  
    align-items: center;

    line-height: 5vh;
    padding: 2vh 2vh;
    margin: 2vh;
    border-radius: 1vh;
    color: white;
    background: linear-gradient(135deg, rgb(67, 24, 255), rgb(134, 140, 255));
}
#total_cmv {
    grid-area: total_cmv;

    display: flex;
    justify-content: space-evenly;  
    align-items: center;

    line-height: 5vh;
    padding: 2vh 2vh;
    margin: 2vh;
    border-radius: 1vh;
    color: #1B2559;
    background: white;
}
#total_lucro_bruto {
    grid-area: total_lucro_bruto;

    display: flex;
    justify-content: space-evenly;  
    align-items: center;

    line-height: 5vh;
    padding: 2vh 2vh;
    margin: 2vh;
    border-radius: 1vh;
    color: #1B2559;
    background: white;
}
#valor_em_locacao {
    grid-area: valor_em_locacao;

    display: flex;
    justify-content: space-evenly;  
    align-items: center;

    line-height: 5vh;
    padding: 2vh 2vh;
    margin: 2vh;
    border-radius: 1vh;
    color: #1B2559;
    background: white;
}
#total_vendas_mensal {
    grid-area: total_vendas_mensal;

    padding: 2vh 2vh;
    margin: 2vh;
    border-radius: 1vh;
    color: #1B2559;
    background: white;
}
#total_vendas_por_agrupamento {
    grid-area: total_vendas_por_agrupamento;
    
    padding: 2vh 2vh;
    margin: 2vh;
    border-radius: 1vh;
    color: #1B2559;
    background: white;
}
#meta_de_vendas {
    grid-area: meta_de_vendas;
    
    padding: 2vh 2vh;
    margin: 2vh;
    border-radius: 1vh;
    color: #1B2559;
    background: white;
}
#ranking_de_vendas_por_vendedor {
    grid-area: ranking_de_vendas_por_vendedor;
    
    padding: 2vh 2vh;
    margin: 2vh;
    border-radius: 1vh;
    color: #1B2559;
    background: white;
}
#ranking_de_vendas_por_cliente {
    grid-area: ranking_de_vendas_por_cliente;
    
    padding: 2vh 2vh;
    margin: 2vh;
    border-radius: 1vh;
    color: #1B2559;
    background: white;
}
#ranking_produtos_mais_vendidos {
    grid-area: ranking_produtos_mais_vendidos;
    
    padding: 2vh 2vh;
    margin: 2vh;
    border-radius: 1vh;
    color: #1B2559;
    background: white;
}








#container_principal {
    width: 90%;
    height: 9vh;
    margin: 0 auto;

    display: flex;
    justify-content: space-between;
    align-items: center;
}
#barra_total {
    background-color: rgba(233, 237, 247, 1);
    width: 70%;
    height: 4vh;
    border-radius: 4vh;
}
#barra_porcentagem {
    background-color: rgba(196, 56, 239, 1);
    height: 4vh;
    border-radius: 4vh;
}
#barra_valor {
    margin-left: 2.5vh !important;
    font-size: 1.5vh;
    display: unset !important;
}
#barra_cliente {
    width: 30%;
}
#meta_de_vendas #container_principal {
    justify-content: center;
    align-items: flex-end;
    flex-direction: column;
    height: 10vh;
}
#meta_de_vendas #barra_cliente  {
    width: 100%;
    text-align: right;
    font-weight: normal;
}
#barra_meta_vendas {
    background-color: rgba(233, 237, 247, 1);
    width: 100%;
    height: 8vh;
    border-radius: 2vh;
}
#barra_meta_vendas #barra_porcentagem {
    height: 8vh; 
    display: flex;
    border-radius: 2vh;
    align-items: center;
}
#barra_meta_vendas #barra_valor {
    color: rgba(233, 237, 247, 1);
    text-align: right; 
}
</style>

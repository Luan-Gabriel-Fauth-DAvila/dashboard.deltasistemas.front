<template>
    <div style="background: #eeeeee;">
        <Navbar :hostBack="hostBack" />
        <div id="painel">
            <div id="res_geral" class="d-flex justify-space-between"> 
                <MiniCard 
                    :label="'Qtd. Total (Produtos em Estoque)'" :value="res_geral.qtdTotalEmEstoque" 
                    :txColor="theme.txColor.light" :bgColor="'linear-gradient(135deg, #05CD99 0%, #05CD55 100%)'"
                    :loading="res_geral.loading" />  
                <MiniCard 
                    :label="'Qtd. Cilindros Cheios'" :value="res_geral.qtdCilindrosCheios" 
                    :txColor="theme.txColor.light" :bgColor="'#6452FF'" 
                    :loading="res_geral.loading" />
                <MiniCard 
                    :label="'Ticket Médio de Compra'" :value="res_geral.ticketMedioDeCompraValor" 
                    :txColor="theme.txColor.light" :bgColor="'#C438EF'" 
                    :loading="res_geral.loading" :side="'left'" />
                <MiniCard 
                    :label="'Valor Total (Produtos em Estoque)'" :value="res_geral.qtdTotalEmEstoqueValor" 
                    :txColor="theme.txColor.light" :bgColor="'#868CFF'" 
                    :loading="res_geral.loading" :side="'left'" />
            </div>
            <v-card :loading="res_geral.loading" id="mapa_locacao">      
                <div id="mapa-title">
                    <h2>Mapa Locações</h2>
                </div> 
                <div id="map"></div>
            </v-card>
            <v-card id="ranking_compra_por_forn">            
                <div>
                    <h2>Ranking de Compras <small>(Por Fornecedor)</small></h2>
                </div> 
                <div :style="{ maxHeight: '40vh' }">
                    <canvas id="ranking_compras_por_fornecedor_chart"></canvas>
                </div>           
            </v-card>
            <v-card id="prod_mais_comprados">            
                <div>
                    <h2>Top 10 Compras Por Produto</h2>
                </div>
                <div :style="{ maxHeight: '40vh' }">
                    <canvas id="ranking_produto_mais_comprado_chart"></canvas>
                </div> 
            </v-card>
        </div>
    </div>
</template>

<script>
import Navbar from '/components/navbar.vue';
import MiniCard from '/components/MiniCard.vue';

export default {
    name: 'Estoque',
    components: {
        Navbar,
        MiniCard
    },
    head () {
        return {
            script: [
                {src: 'https://unpkg.com/leaflet@1.8.0/dist/leaflet.js'},
            ],
            link: [
                { rel: 'stylesheet', type: '', href: 'https://unpkg.com/leaflet@1.8.0/dist/leaflet.css' },
            ]
        }
    },
    data () {
        return {
            hostBack: process.env.HOST_BACK,
            hostFront: process.env.HOST_FRONT,
            colors: [
                '#c438ef',
                '#05cd99',
                '#4318ff',
                '#ffc086',
                '#ff409a',
                '#6452ff',
                '#05cd99',
            ],
            latlon: '',

            res_geral: {
                qtdCilindrosCheios: null,
                ticketMedioDeCompraValor: null,
                qtdTotalEmEstoque: null,
                qtdTotalEmEstoqueValor: null,
                loading: false,
            },
            theme: {
                txColor: {
                    dark: '#1b2559',
                    light: '#fff',
                },
                pallete: [
                    '#c438ef',
                    '#ff409a',
                    '#6452ff',
                    '#05cd99',
                    '#6452ff',
                    '#ffc086',
                    '#4318ff',
                    '#05cd99',
                ]
            },
        }
    },
    methods: {
        moneyFilter (msg){
            return parseFloat(msg).toLocaleString('pt-BR', { style: 'currency', currency: 'BRL' })
        },
        
        tamanhoBarra (barra,total){
            const tamanho = parseFloat(parseInt(parseFloat(barra)/parseFloat(total)*100))
            if (tamanho < 10) {return 10}
            return tamanho;
        },

        async res_geral_estoque () {
            const req = await fetch(this.hostBack+'/res_geral_estoque')

            if (req.status == 200) {
                const res = await req.json()
                this.res_geral.qtdCilindrosCheios = res.qtd_cilindros_cheios
                this.res_geral.ticketMedioDeCompraValor = this.moneyFilter(res.media_compras)
                this.res_geral.qtdTotalEmEstoqueValor = this.moneyFilter(res.valor_produtos_em_estoque)
                this.res_geral.qtdTotalEmEstoque = res.qtd_produtos_em_estoque
            }
        },

        async map() {
            this.res_geral.loading = true
            const req_con = await fetch(this.hostBack+'/condicionais_abertas/')
            const res_con = await req_con.json()
            const req = await fetch(this.hostBack+'/mapa_de_locacoes/')
            const res = await req.json()

            var map = L.map('map').setView([res_con[0].lat, res_con[0].lon], 13);
            var myIcon = L.icon({
                iconUrl: '/mapicon.png',
                iconSize: [60, 60],
                iconAnchor: [30, 60],
                popupAnchor: [0, -60],
            });
            for (let i = 0; i < res_con.length; i++) {
                let cond = res.filter((res) => {return res['numcondicional'] == res_con[i]['numcondicional'] })
                console.log(cond)
                let itens = ''
                let preco = 0
                cond.forEach((c) => {
                    preco = preco + parseFloat(c.preco)
                    itens = itens + `
                    <tr>
                        <td id="produto">`+c.dscproduto+`</td>
                        <td id="qtd_locada">`+c.qtd_locada+`</td>
                        <td id="valor">`+this.moneyFilter(c.preco)+`</td>
                    </tr>
                    `
                })
                L.marker([res_con[i].lat, res_con[i].lon],{icon: myIcon, alt: ''}).addTo(map).bindPopup(`
                <div id='popupPointMap'>
                    <div id="date">Data de Locação `+res_con[i].dtacomp+`</div>
                    <p id='title'>`+res_con[i].nome+`</p>
                    <table id='product'>
                        <thead>
                            <tr>
                                <th id="produto">Produto</th>
                                <th id="qtd_locada">Qtd</th>
                                <th id="valor">Valor</th>
                            </tr>
                        </thead>
                        <tbody>
                            `+itens+`
                        </tbody>
                    </table>
                    <div id='result'>
                        <p>Valor Total:</p>
                        <p>`+this.moneyFilter(preco)+`</p>
                    </div>
                </div>
                `);
            }
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
                maxZoom: 19,
            }).addTo(map);
            this.res_geral.loading = false
        },
        async rankingComprasPorFornecedor () {
            const req = await fetch(this.hostBack+'/ranking_compras_por_fornecedor')
            const res = await req.json()

            const formaRecebimentoTitle = res.nome
            const formaRecebimentosValue = res.valor_nota
            //GRÁFICO
            const data = {
                labels: formaRecebimentoTitle,
                datasets: [{
                    label: 'Valor de Vendas Mensais',
                    data: formaRecebimentosValue,
                    backgroundColor: this.colors,
                    hoverOffset: 4,
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
            var chartStatus = Chart.getChart("ranking_compras_por_fornecedor_chart"); // <canvas> id
            if (chartStatus != undefined) {
                chartStatus.data.datasets.data = res.total_vendas
                chartStatus.update()
            }else {
                const total_por_agrupamento = new Chart(
                    document.getElementById('ranking_compras_por_fornecedor_chart'),
                    config
                );
            }
        },

        async rankingProdutoMaisComprado () {
            const req = await fetch(this.hostBack+'/ranking_produto_mais_comprado')
            const res = await req.json()

            const formaRecebimentoTitle = res.dscproduto
            const formaRecebimentosValue = res.quantidade
            //GRÁFICO
            const data = {
                labels: formaRecebimentoTitle,
                datasets: [{
                    label: 'Produto mais Comprado',
                    data: formaRecebimentosValue,
                    backgroundColor: this.colors,
                    hoverOffset: 4,
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
            var chartStatus = Chart.getChart("ranking_produto_mais_comprado_chart"); // <canvas> id
            if (chartStatus != undefined) {
                chartStatus.data.datasets.data = res.total_vendas
                chartStatus.update()
            }else {
                const total_por_agrupamento = new Chart(
                    document.getElementById('ranking_produto_mais_comprado_chart'),
                    config
                );
            }
        }
    },
    mounted () {
        this.map()
        this.res_geral_estoque()
        this.rankingComprasPorFornecedor()
        this.rankingProdutoMaisComprado ()
        setInterval(() => {
            this.res_geral_estoque()
            this.rankingComprasPorFornecedor()
            this.rankingProdutoMaisComprado ()
        },10000)
    },
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
h4 {
    line-height: 2.5vh;
}
h2 {
    font-size: 3vh;
    line-height: 3vh;
}
small {
    line-height: 2.5vh;
    font-size: 1vh;
}
h6 {
    margin-left: 1vh;
    font-size: 1.5vh;
    font-weight: bold;
}
label {
    margin: unset;
    padding: unset;
    line-height: 2.5vh;
    font-size: 1.3vh;

    display: flex;
    align-items: center;
}

#painel {
    display: grid;
    grid-template-columns: 25% 25% 25% 25%;
    grid-template-rows: 15vh 80vh 50vh;
    grid-template-areas:    "res_geral res_geral res_geral res_geral"
                            "mapa_locacao mapa_locacao mapa_locacao mapa_locacao "
                            "ranking_compra_por_forn ranking_compra_por_forn prod_mais_comprados prod_mais_comprados";
    margin: 0 10%;  
    padding: 5% 0; 
}
@media only screen and (max-width: 630px) {
  #painel {
        display: grid;
        grid-template-columns: 50% 50%;
        grid-template-rows: 15vh 15vh 80vh 50vh 50vh;
        grid-template-areas:    "prod_em_estoque cilindros_cheios"
                                "ticket_medio_de_compra vlr_prod_em_estoque"
                                "mapa_locacao mapa_locacao"
                                "ranking_compra_por_forn ranking_compra_por_forn"
                                "prod_mais_comprados prod_mais_comprados";
        margin: 0;  
        padding: 5% 0;
   
    }
}
#res_geral {
    grid-area: res_geral;
    margin: 2vh;
}
#mapa_locacao {
    grid-area: mapa_locacao;
    color: #1B2559;
    background-color: white;
    margin: 2vh;
    padding: 2vh 2vh;
}
#mapa-title {
    margin-bottom: 1vh;
}
#map { 
    height: 69vh; 
    border-radius: 2vh;
}
.leaflet-popup-tip {
    background-color: #6b7ff3;
}
.leaflet-popup-content-wrapper {
    background-color: transparent;
    box-shadow: unset;
    background-image: url('https://www.meusistemafinanceiro.com.br/static/public/estoque/map_popup_bg.svg');
    background-position: center;
    background-size: cover;
    width: 300px;
    height: 250px;
}
#ranking_compra_por_forn {
    grid-area: ranking_compra_por_forn;
    color: #1B2559 !important;
    background-color: white;
    margin: 2vh;
    padding: 2vh 2vh;
}
#prod_mais_comprados {
    grid-area: prod_mais_comprados;
    color: #1B2559;
    background-color: white;
    margin: 2vh;
    padding: 2vh 2vh;
}

#popupPointMap {
    display: grid; 
    grid-template-columns: 100%; 
    grid-template-rows: 25px 85px 85px 40px; 
    grid-template-areas: 'date' 'title' 'product' 'result';

    color: #1B2559;
    z-index: 0;
}
table, th, td {
    border-collapse: collapse;
}
thead tr {
    border-bottom: 1.5px solid #a8afd0;
}
tbody tr {
    border-bottom: 1px solid #a8afd0;
}
#popupPointMap #date {
    grid-area: date; 
    line-height: 30px; 
    font-size: 8px;
}
#popupPointMap #title {
    grid-area: title; 
    font-weight: 800;
}
#popupPointMap #product {
    grid-area: product;

    display: flex; 
    flex-direction: column; 
    justify-content: center; 
    align-items: center;
}
#popupPointMap #result {
    grid-area: result;

    display: flex; 
    justify-content: space-between; 
    align-items: center;

    color: #fff;
}
#popupPointMap #product #produto {
    text-align: left;
    width: 22vw;    
    font-size: 1.7vh;
}
#popupPointMap #product #qtd_locada {
    text-align: center;
    width: 3vw;
    font-size: 1.7vh;
}
#popupPointMap #product #valor {
    text-align: center;
    width: 8vw;
    font-size: 1.7vh;
}
</style>
<template>
    <div>
        <Navbar />
        <div id="painel">
            <div id="prod_em_estoque">                        
                <div>
                    <label>Qtd. Total (Produtos em Estoque)</label>
                </div>
                <div>
                    <h4 id="prod_em_estoque_value">Sem dados</h4>
                </div>   
            </div>
            <div id="cilindros_cheios">            
                <div>
                    <label>Qtd. Cilindros Cheios</label>
                </div>  
                <div>
                    <h4 id="cilindros_cheios_value">Sem Dados</h4>
                </div>             
            </div>
            <div id="ticket_medio_de_compra">            
                <div>
                    <label>Ticket Médio de Compra</label>
                </div>   
                <div>
                    <h4 id="ticket_medio_de_compra_value">Sem dados</h4>
                </div>            
            </div>
            <div id="vlr_prod_em_estoque">            
                <div>
                    <label>Valor Total (Produtos em Estoque)</label>
                </div> 
                <div>
                    <h4 id="vlr_prod_em_estoque_value">Sem dados</h4>
                </div>           
            </div>
            <div id="mapa_locacao">      
                <div id="mapa-title">
                    <h2>Mapa Locações</h2>
                </div> 
                <div id="mapa-img" style="background-image: url('http://127.0.0.1:8000/static/public/map-lock.png');"></div>
            </div>
            <div id="ranking_compra_por_forn">            
                <div>
                    <h2>Ranking de Compras <small>(Por Fornecedor)</small></h2>
                </div> 
                <div :style="{ maxHeight: '40vh' }">
                    <canvas id="ranking_compras_por_fornecedor_chart"></canvas>
                </div>           
            </div>
            <div id="prod_mais_comprados">            
                <div>
                    <h2>Top 10 Compras Por Produto</h2>
                </div>
                <div :style="{ maxHeight: '40vh' }">
                    <canvas id="ranking_produto_mais_comprado_chart"></canvas>
                </div> 
            </div>
        </div>
    </div>
</template>

<script>
import Navbar from '/components/navbar.vue';

export default {
    name: 'Estoque',
    components: {
        Navbar,
    },
    data () {
        return {
            hostBackEnd: 'http://188.166.65.228:8000',
            colors: [
                '#c438ef',
                '#05cd99',
                '#4318ff',
                '#ffc086',
                '#ff409a',
                '#6452ff',
                '#05cd99',
            ],
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

        async qtdCilindrosCheios () {
            const req = await fetch(this.hostBackEnd+'/qtd_cilindros_cheios')
            const res = await req.json()

            self.cilindros_cheios_value = res.qtd[0]
            
        },

        async ticketMedioDeCompra () {
            const req = await fetch(this.hostBackEnd+'/ticket_medio_de_compra')
            const res = await req.json()
            
            if (res[0].media_compras != 'None') {
                self.ticket_medio_de_compra_value.innerHTML = this.moneyFilter(res[0].media_compras)
            }
        },

        async valorDeTodosOsProdutos () {
            const req = await fetch(this.hostBackEnd+'/total_de_valores_de_todos_os_produtos')
            const res = await req.json()
            
            self.prod_em_estoque_value.innerHTML = res.quantidade
            self.vlr_prod_em_estoque_value.innerHTML = this.moneyFilter(res.valor_total)
        },

        async rankingComprasPorFornecedor () {
            const req = await fetch(this.hostBackEnd+'/ranking_compras_por_fornecedor')
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
            const req = await fetch(this.hostBackEnd+'/ranking_produto_mais_comprado')
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
        this.ticketMedioDeCompra()
        this.valorDeTodosOsProdutos()
        this.rankingComprasPorFornecedor()
        this.rankingProdutoMaisComprado ()
        setInterval(() => {
            this.ticketMedioDeCompra()
            this.valorDeTodosOsProdutos()
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
    grid-template-areas:    "prod_em_estoque cilindros_cheios ticket_medio_de_compra vlr_prod_em_estoque"
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
#prod_em_estoque {
    grid-area: prod_em_estoque;
    color: #e5e5e5;
    background: linear-gradient(135deg, #05CD99 0%, #05CD55 100%);
    margin: 2vh;
    border-radius: 2vh;
    padding: 2vh 2vh;
    padding-bottom: 4vh;

    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
    align-items: center;
}
#cilindros_cheios {
    grid-area: cilindros_cheios;
    color: #e5e5e5;
    background: #6452FF;
    margin: 2vh;
    border-radius: 2vh;
    padding: 2vh 2vh;
    padding-bottom: 4vh;

    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
    align-items: center;
}
#ticket_medio_de_compra {
    grid-area: ticket_medio_de_compra;
    color: #e5e5e5;
    background: #C438EF;
    margin: 2vh;
    border-radius: 2vh;
    padding: 2vh 2vh;
    padding-bottom: 4vh;

    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
    align-items: center;
}
#vlr_prod_em_estoque {
    grid-area: vlr_prod_em_estoque;
    color: #e5e5e5;
    background: #868CFF;
    margin: 2vh;
    border-radius: 2vh;
    padding: 2vh 2vh;
    padding-bottom: 4vh;

    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
    align-items: center;
}
#mapa_locacao {
    grid-area: mapa_locacao;
    color: #1B2559;
    background-color: white;
    margin: 2vh;
    border-radius: 2vh;
    padding: 2vh 2vh;
}
#mapa-img {
    width: 90%;
    height: 90%;
    border-radius: 2vh;
    margin: auto auto;
    background-size: cover;
    background-position: center;
}
#mapa-title {
    margin-bottom: 2vh;
}
#ranking_compra_por_forn {
    grid-area: ranking_compra_por_forn;
    color: #1B2559;
    background-color: white;
    margin: 2vh;
    border-radius: 2vh;
    padding: 2vh 2vh;
}
#prod_mais_comprados {
    grid-area: prod_mais_comprados;
    color: #1B2559;
    background-color: white;
    margin: 2vh;
    border-radius: 2vh;
    padding: 2vh 2vh;
}
</style>
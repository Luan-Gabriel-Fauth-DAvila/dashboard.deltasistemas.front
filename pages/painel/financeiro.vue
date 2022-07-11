<template>
    <div>
        <Navbar :hostBack="hostBack" />
        <section id="painel">
            <!-- <div id="filter">
                <div>
                    <label for="">Data Inicio</label>
                    <input type="date" id="data_ini">
                </div>
                <div>
                    <label for="">Data Fim</label>
                    <input type="date" id="data_fim">
                </div>
                <button @click="defData" id="buttom_filter">Aplicar</button>
            </div> -->
            <div id="saldo_em_conta">
                <div>
                    <h4>Saldo em Conta</h4>
                </div>
                <div id="banco_data">
                    <div id="banco_total">
                        <label>Total</label>
                        <img :src="hostBack+'/static/public/bancos/wallet.svg'" :style="{ width: '12vh' }" alt="">
                        <h6 v-if="saldoDisponivelEmContas_sum">{{ moneyFilter(saldoDisponivelEmContas_sum) }}</h6>
                        <h6 v-else>Sem Dados</h6>
                    </div>
                    <div id="banco" v-for="(c, index) in saldoDisponivelEmContas_var" :key="index">
                        <label>{{ c.nome_banco.toLowerCase() }}</label>
                        <img v-if="c.banco === '1'" :src="hostBack+'/static/public/bancos/bb.png'" :style="{ width: '11vh' }" alt="">
                        <img v-else-if="c.banco === '260'" :src="hostBack+'/static/public/bancos/nu.png'" :style="{ width: '11vh' }" alt="">
                        <img v-else-if="c.banco === '33'" :src="hostBack+'/static/public/bancos/santander.svg'" :style="{ width: '11vh' }" alt="">
                        <img v-else-if="c.banco === '748'" :src="hostBack+'/static/public/bancos/sicredi.svg'" :style="{ width: '11vh' }" alt="">
                        <img v-else-if="c.banco === '756'" :src="hostBack+'/static/public/bancos/sicoob.svg'" :style="{ width: '11vh' }" alt="">
                        <img v-else :src="hostBack+'/static/public/bancos/wallet.svg'" :style="{ width: '12vh' }" alt="">
                        <label>{{ moneyFilter(c.valor) }}</label>
                    </div>
                </div>
            </div>
            <div id="cr_atrasado">
                <div id="cpr_title">
                    <label>Contas a receber</label><small>(atrasado)</small>
                </div>
                <div id="cpr_value">
                    <h4 id="cr_atrasado_value">Sem dados</h4>
                </div>
            </div>
            <div id="cr_vencer">
                <div id="cpr_title">
                    <label>Contas a receber</label><small>(a vencer)</small>
                </div>
                <div id="cpr_value">
                    <h4 id="cr_vencer_value">Sem dados</h4>
                </div>
            </div>
            <div id="cp_atrasado">
                <div id="cpr_title">
                    <label>Contas a pagar</label><small>(atrasado)</small>
                </div>
                <div id="cpr_value">
                    <h4 id="cp_atrasado_value">Sem dados</h4>
                </div>
            </div>
            <div id="cp_vencer">
                <div id="cpr_title">
                    <label>Contas a pagar</label><small>(a vencer)</small>
                </div>
                <div id="cpr_value">
                    <h4 id="cp_vencer_value">Sem dados</h4>
                </div>
            </div>
            <div id="contas_a_receber">
                <div>
                    <h4>Contas a Receber <small>(dia)</small></h4>
                </div>
                <div :style="{ height: '50vh' }">
                    <canvas id="contas_a_receber_chart"></canvas>
                </div>
            </div>
            <div id="contas_a_pagar">
                <div>
                    <h4>Contas a Pagar <small>(dia)</small></h4>
                </div>
                <div :style="{ height: '50vh' }">
                    <canvas id="contas_a_pagar_chart"></canvas>
                </div>
            </div>
            <div id="recebimentos_por_forma">
                <div>
                    <h4>Recebimentos por forma</h4>
                </div>
                <div style="max-height: 30vh;">
                    <canvas id="recebimentos_por_forma_chart"></canvas>
                </div>
            </div>
            <div id="emprestimos">
                <div id="emprestimos_title">
                    <h4>Empréstimos <small>(saldo)</small></h4><h3 :style="{ color: '#FF869C', fontSize: '3vh' }">{{ detalhesFinaciamentoEmprestimo_total }}</h3>
                </div>
                <div>
                    <div id="container_principal" v-for="(r, index) in detalhesFinaciamentoEmprestimo_var" :key="index">
                        <div id='barra_total'>
                            <div id="barra_porcentagem" :style="{ width: tamanhoBarra(r.valor, detalhesFinaciamentoEmprestimo_sum) + '%' }">
                                <p>
                                    <b id="barra_valor">{{ moneyFilter(r.valor) }}</b> | <b id="barra_cliente"> {{ r.nome }}</b> 
                                </p>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div id="fluxo_de_caixa">
                <div>
                    <h4>Previsão de Faturas <small>(para os próximos 20 dias)</small></h4>
                </div>
                <div :style="{ height: '30vh' }">
                    <div id="fluxo_20_dias_chart"></div>
                </div>
            </div>
        </section>
    </div>
</template>

<script>
import Navbar from '/components/navbar.vue';
export default {
    name: 'Financeiro',
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
            colors: [
                '#c438ef',
                '#05cd99',
                '#4318ff',
                '#ffc086',
                '#ff409a',
                '#6452ff',
                '#05cd99',
            ],
            detalhesFinaciamentoEmprestimo_var: null,
            detalhesFinaciamentoEmprestimo_sum: null,
            detalhesFinaciamentoEmprestimo_total: null, 

            saldoDisponivelEmContas_var: null,
            saldoDisponivelEmContas_sum: null,
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

        async saldoDisponivelEmContas () {
            const req = await fetch(this.hostBackEnd+'/saldo_disponivel_em_contas')
            const res = await req.json()

            this.saldoDisponivelEmContas_var = res
            let sum = 0
            for (let i = 0; i < res.length; i++) {
                sum = sum + parseFloat(res[i].valor)
            }
            this.saldoDisponivelEmContas_sum = sum
        },
        defData () {
            const ini = self.data_ini.value.split('-', 3)
            const fim = self.data_fim.value.split('-', 3)
            window.location.href = ('http://localhost:3000/painel/financeiro/?data_ini='+ini[2]+'.'+ini[1]+'.'+ini[0]+'&data_fim='+fim[2]+'.'+fim[1]+'.'+fim[0])
        },
        defFilter () {
            const urlParams = new URLSearchParams(window.location.search);
            const data_ini = urlParams.get('data_ini')
            const data_fim = urlParams.get('data_fim')
            const urlFilter = '?data_ini='+data_ini+'&data_fim='+data_fim
            return urlFilter
        },

        async contasReceberAtrasadas () {
            const req = await fetch(this.hostBackEnd+'/contas_a_receber_do_inicio_ate_a_data_atual')
            const res = await req.json()
            
            self.cr_atrasado_value.innerHTML = this.moneyFilter(res.faturas_receber)
        },

        async contasReceberAVencer () {
            const req = await fetch(this.hostBackEnd+'/contas_a_receber_a_partir_da_data_atual')
            const res = await req.json()

            self.cr_vencer_value.innerHTML = this.moneyFilter(res.faturas_receber)
        },

        async contasPagarAtrasadas () {
            const req = await fetch(this.hostBackEnd+'/contas_a_pagar_do_inicio_ate_a_data_atual')
            const res = await req.json()
            
            self.cp_atrasado_value.innerHTML = this.moneyFilter(res.faturas_pagar)
        },

        async contasPagarAVencer () {
            const req = await fetch(this.hostBackEnd+'/contas_a_pagar_a_partir_da_data_atual')
            const res = await req.json()

            self.cp_vencer_value.innerHTML = this.moneyFilter(res.faturas_pagar)
        },

        async contasReceber () {
            const req = await fetch(this.hostBackEnd+'/contas_a_receber_por_ranking_e_dia')
            const res = await req.json()

            let parceiro = []
            res.parceiro.forEach(function (e) {
                parceiro.push(e.substr(0,10))
            })
            const data = {
                labels: parceiro,
                datasets: [{
                    data: res.saldo,
                    label: 'Constas a Receber',
                    borderColor: '#05CD99',
                    backgroundColor: '#05CD99',
                    borderWidth: 2,
                    borderRadius: '5px',
                    borderSkipped: false,
                }]
            };
            const config = {
            type: 'bar',
            data: data,
                options: {
                    maintainAspectRatio: false,
                    responsive: true,
                    plugins: {
                        legend: {
                            position: 'top',
                        },
                        title: {
                            display: false,
                        }
                    }
                },
            };
            var chartStatus = Chart.getChart("contas_a_receber_chart"); // <canvas> id
            if (chartStatus != undefined) {
                chartStatus.data.datasets.data = res.total_vendas
                chartStatus.update()
            }else {
                const contas_a_receber = new Chart(
                    document.getElementById('contas_a_receber_chart'),
                    config
                );
            }
        },

        async contasPagar () {
            const req = await fetch(this.hostBackEnd+'/contas_a_pagar_por_ranking_e_dia')
            const res = await req.json()

            let parceiro = []
            res.parceiro.forEach(function (e) {
                parceiro.push(e.substr(0,10))
            })

            const data = {
                labels: parceiro,
                datasets: [{
                    data: res.saldo,
                    label: 'Constas a Pagar',
                    borderColor: '#FF869C',
                    backgroundColor: '#FF869C',
                    borderWidth: 2,
                    maxBarThickness: 64,

                    borderRadius: '10px',
                    borderSkipped: false,
                }]
            };
            const config = {
            type: 'bar',
            data: data,
                options: {
                    maintainAspectRatio: false,
                    responsive: true,
                    plugins: {
                        legend: {
                            position: 'top',
                        },
                        title: {
                            display: false,
                        }
                    }
                },
            };
            var chartStatus = Chart.getChart("contas_a_pagar_chart"); // <canvas> id
            if (chartStatus != undefined) {
                chartStatus.data.datasets.data = res.total_vendas
                chartStatus.update()
            }else {
                const total_vendas_mensal = new Chart(
                    document.getElementById('contas_a_pagar_chart'),
                    config
                );
            }
        },

        async recebimentosPorForma () {
            const req = await fetch(this.hostBackEnd+'/total_de_recebimentos_por_forma_mensal/resumo_geral')
            const res = await req.json()

            const formaRecebimentoTitle = [
                'Recebimentos Cartão',
                'Recebimentos em Dinheiro',
                'Recebimentos',
                'Cheques Recebidos',
                'Entradas em Banco',
            ]
            const formaRecebimentosValue = [
                res.rec_cartao,
                res.rec_dinheiro,
                res.recebimentos,
                res.cheques_recebidos,
                res.entradas_bancos,
            ]
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
            var chartStatus = Chart.getChart("recebimentos_por_forma_chart"); // <canvas> id
            if (chartStatus != undefined) {
                chartStatus.data.datasets.data = res.total_vendas
                chartStatus.update()
            }else {
                const total_por_agrupamento = new Chart(
                    document.getElementById('recebimentos_por_forma_chart'),
                    config
                );
            }
            //FIM GRÁFICO
        },

        async detalhesFinaciamentoEmprestimo () {
            const req = await fetch(this.hostBackEnd+'/detalhes_finaciamento')
            const res = await req.json()
            
            this.detalhesFinaciamentoEmprestimo_var = res
            this.detalhesFinaciamentoEmprestimo_sum = parseFloat(res[0].valor)

            let value = 0
            for (let i = 0; i < res.length; i++) {
                value = value + parseFloat(res[i].valor)
            }
            this.detalhesFinaciamentoEmprestimo_total = this.moneyFilter(value)
        },

        async fluxoDeCaixa () {
            const req = await fetch(this.hostBackEnd+'/fluxo_de_caixa')
            const res = await req.json()

            const total_vendas_mensal_chart = Highcharts.chart('fluxo_20_dias_chart', {
                plotOptions: {
                    series: {
                        // general options for all series
                    },
                    spline: {
                    },
                },
                chart: {
                    height: parseInt(window.screen.height)*0.32
                },
                title: {
                    text: undefined,
                },
                xAxis: {
                    categories: res.data,
                },
                yAxis: {
                    title: {
                        text: undefined,
                    }
                },
                series: [{
                    type: 'spline',
                    name: 'Contas a Pagar',
                    data: res.contas_pagar,
                    color: '#FF869C',
                },{
                    type: 'spline',
                    name: 'Contas a Receber',
                    data: res.contas_receber,
                    color: '#05CD99',
                }]
            })
        }
    },
    mounted () {
        this.saldoDisponivelEmContas()
        this.contasReceberAtrasadas()
        this.contasReceberAVencer()
        this.contasPagarAtrasadas()
        this.contasPagarAVencer()
        this.contasReceber()
        this.contasPagar()
        this.recebimentosPorForma()
        this.detalhesFinaciamentoEmprestimo()
        this.fluxoDeCaixa()
        setInterval(() => {
            this.saldoDisponivelEmContas()
            this.contasReceberAtrasadas()            
            this.contasReceberAVencer()
            this.contasPagarAtrasadas()
            this.contasPagarAVencer()
            this.contasReceber()
            this.contasPagar()
            this.recebimentosPorForma()
            this.detalhesFinaciamentoEmprestimo()
            this.fluxoDeCaixa()
        }, 10000)
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


/* Gráfico de Barra */
#container_principal {
    width: 90%;
    height: 4.5vh;
    margin: 0 auto;

    display: flex;
    justify-content: space-between;
    align-items: center;
}
#barra_total {
    background-color: rgba(233, 237, 247, 1);
    width: 100%;
    height: 3vh;
    border-radius: 4vh;
}
#barra_porcentagem {
    background-color: #FF869C;
    height: 3vh;
    border-radius: 4vh;
    white-space: nowrap;
}
#barra_valor {
    margin-left: 2.5vh !important;
    font-size: 1.5vh;
    display: unset !important;
}
#barra_cliente {
    width: 50%;
    font-size: 1.5vh;
}


/* Fim Gráfico de Barra */


#painel {
    display: grid;
    grid-template-columns: 25% 25% 25% 25%;
    grid-template-rows: 30vh 15vh 60vh 40vh 50vh;
    grid-template-areas:    /*"filter filter filter filter"*/
                            "saldo_em_conta saldo_em_conta saldo_em_conta saldo_em_conta"
                            "cr_atrasado cr_vencer cp_atrasado cp_vencer"
                            "contas_a_receber contas_a_receber contas_a_pagar contas_a_pagar"
                            "recebimentos_por_forma recebimentos_por_forma emprestimos emprestimos"
                            "fluxo_de_caixa fluxo_de_caixa fluxo_de_caixa fluxo_de_caixa";
    margin: 0 10%;  
    padding: 5% 0; 
}
@media only screen and (max-width: 630px) {
  #painel {
        display: grid;
        grid-template-columns: 50% 50%;
        grid-template-rows: 30vh 15vh 15vh 60vh 60vh 40vh 50vh;
        grid-template-areas:    /*"filter filter"
                                "filter filter"*/
                                "saldo_em_conta saldo_em_conta"
                                "cr_atrasado cr_vencer"
                                "cp_atrasado cp_vencer"
                                "contas_a_receber contas_a_receber"
                                "contas_a_pagar contas_a_pagar"
                                "recebimentos_por_forma recebimentos_por_forma"
                                "emprestimos emprestimos"
                                "fluxo_de_caixa fluxo_de_caixa";
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
#saldo_em_conta {
    grid-area: saldo_em_conta;
    color: #1B2559;
    background-color: white;
    margin: 2vh;
    border-radius: 2vh;
    padding: 2vh 2vh;

    display: flex;
    flex-direction: column;
}
#banco_data {
    display: flex;
    justify-content: flex-start;
    align-items: center;
    height: 100%;
    overflow-x: auto;
    overflow-y: hidden;
}
#banco_total {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 15vh;
    height: 15vh;
    margin: 2vh;
    /* background-color: #e5e5e5; */
    border-radius: 2vh;
    margin-right: 15vh;
}
#banco {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    min-width: 12vh;
    height: 15vh;
    margin: 2vh;
    /* background-color: #e5e5e5; */
    border-radius: 2vh;
}





#cr_atrasado {
    grid-area: cr_atrasado;
    color: #E5E5E5;
    margin: 2vh;
    border-radius: 2vh;
    padding: .5vh 2vh 2vh 2vh;

    background: linear-gradient(135deg, #05CD99 0%, #05CD55 100%);
    display: flex;
    flex-direction: column;
    justify-content: space-evenly;

}
#cpr_title {
    width: 100%;
    text-align: center;
}
#cpr_value {
    width: 100%;
    text-align: center;
}
#cr_vencer {
    grid-area: cr_vencer;
    color: #E5E5E5;
    margin: 2vh;
    border-radius: 2vh;
    padding: .5vh 2vh 2vh 2vh;

    background: linear-gradient(135deg, #05CD99 0%, #05CD55 100%);
    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
}
#cp_atrasado {
    grid-area: cp_atrasado;
    color: #E5E5E5;
    margin: 2vh;
    border-radius: 2vh;
    padding: .5vh 2vh 2vh 2vh;
    
    background: linear-gradient(135deg, #CD0511 0%, #FF409A 94.06%);
    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
}
#cp_vencer {
    grid-area: cp_vencer;
    color: #E5E5E5;
    margin: 2vh;
    border-radius: 2vh;
    padding: .5vh 2vh 2vh 2vh;
    
    background: linear-gradient(135deg, #CD0511 0%, #FF409A 94.06%);
    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
}





#contas_a_receber {
    grid-area: contas_a_receber;
    color: #1B2559;
    background-color: white;
    margin: 2vh;
    border-radius: 2vh;
    padding: 2vh 2vh;
    
}
#contas_a_pagar {
    grid-area: contas_a_pagar;
    color: #1B2559;
    background-color: white;
    margin: 2vh;
    border-radius: 2vh;
    padding: 2vh 2vh;
    
}
#recebimentos_por_forma {
    grid-area: recebimentos_por_forma;
    color: #1B2559;
    background-color: white;
    margin: 2vh;
    border-radius: 2vh;
    padding: 2vh 2vh;
    
}


#emprestimos {
    grid-area: emprestimos;
    color: #1B2559;
    background-color: white;
    margin: 2vh;
    border-radius: 2vh;
    padding: 2vh 2vh;
    
}
#emprestimos_title {
    display: flex;
    justify-content: space-between;
    align-items: center;
}


#fluxo_de_caixa {
    grid-area: fluxo_de_caixa;
    color: #1B2559;
    background-color: white;
    margin: 2vh;
    border-radius: 2vh;
    padding: 2vh 2vh;
    
}
</style>
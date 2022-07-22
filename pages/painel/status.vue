<template>
    <div>
        <Navbar :hostBack="hostBack" />
        <section id="painel">
            <div id="notas_nao_emitidas">
                <div id="title">
                    <h2>Notas Fiscais</h2>
                    <label for="">Não emitidas</label>
                </div>
                <div id="content">
                    <table>
                        <thead>
                            <tr>
                                <th id="numnota">Nº Nota</th>
                                <th id="status">Status</th>
                                <th id="erro">Erro</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="n in notasnaoemitidas" :key="n.num_nota">
                                <td id="numnota">{{ n.num_nota }}</td>
                                <td id="status">{{ n.codretorno }}</td>
                                <td id="erro">{{ n.retorno }}</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
            <div id="certificado_digital">
                <div id="title">
                    <h2>Validade do Certificado Digital</h2>
                </div>
                <div id="content">
                    <h4 v-for="valid in validadeCertificadoDigital" :key='valid.codfilial'>
                        <h4><b>Valido até:</b> {{ valid.vencimento }}</h4>
                        <h4><b>Filial:</b> {{ valid.codfilial }}</h4>
                    </h4>
                </div>
            </div>
        </section>
    </div>
</template>

<script>
import Navbar from '/components/navbar.vue';
export default {
    name: 'Status',
    components: {
        Navbar,
    },
    data () {
        return {
            hostBack: process.env.HOST_BACK,
            hostFront: process.env.HOST_FRONT,
            notasnaoemitidas: null,
            validadeCertificadoDigital: null,
        }
    },
    head() {
        return {
            script: [
                {src: 'https://cdn.jsdelivr.net/npm/chart.js'},
                {src: "https://code.highcharts.com/highcharts.js",body: true},
                {src: "https://code.highcharts.com/modules/accessibility.js",body: true},
                {href: "", rel:"stylesheet", integrity:"sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO", crossorigin:"anonymous"}
            ]
        }
    },
    methods: {
        async funcNotasNaoEmitidas() {
            const req = await fetch(this.hostBack+'/notas_nao_emitidas')
            const res = await req.json()

            this.notasnaoemitidas = res
        },
        async certificadoDigital() {
            const req = await fetch(this.hostBack+'/certificado_digital')
            const res = await req.json()

            this.validadeCertificadoDigital = res
        }
    },
    mounted() {
        this.funcNotasNaoEmitidas()
        this.certificadoDigital()
        setInterval(() => {
            this.funcNotasNaoEmitidas()
            this.certificadoDigital()
        }, 30000)
    }
    
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300&display=swap');
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-size: 2vh;
    font-family: 'Poppins', sans-serif;
}
html,body {
    background-color: #eeeeee !important;
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
    font-weight: 100;
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
    grid-template-areas:    "notas_nao_emitidas notas_nao_emitidas certificado_digital certificado_digital"
                            "notas_nao_emitidas notas_nao_emitidas total_lucro_bruto valor_em_locacao"
                            "notas_nao_emitidas notas_nao_emitidas total_vendas_por_agrupamento total_vendas_por_agrupamento"
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
        grid-template-areas:    "notas_nao_emitidas notas_nao_emitidas"
                                "notas_nao_emitidas notas_nao_emitidas"
                                "notas_nao_emitidas notas_nao_emitidas" 
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
#notas_nao_emitidas {
    grid-area: notas_nao_emitidas;

    display: grid;
    grid-template-columns: 100%;
    grid-template-rows: 10% 90%;
    grid-template-areas:    "title"
                            "content";

    background: #fff;
    padding: 2vh 2vh;
    margin: 2vh;
    border-radius: .5vh;
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
#notas_nao_emitidas #title {
    grid-area: title;
}
#notas_nao_emitidas #content {
    grid-area: content;
}
#notas_nao_emitidas #content #numnota {
    text-align: center;
    width: 9vw;
}
#notas_nao_emitidas #content #status {
    text-align: center;
    width: 6vw;
}
#notas_nao_emitidas #content #erro {
    text-align: center;
    width: 20vw;
}
#certificado_digital {
    grid-area: certificado_digital;

    display: flex;
    flex-direction: column;
    justify-content: center;
    background: #fff;
    padding: 2vh 2vh;
    margin: 2vh;
    border-radius: .5vh;
    color: #1B2559;
    z-index: 0;
}
</style>
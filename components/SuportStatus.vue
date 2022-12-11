<template>
    <v-dialog v-model="dialog" max-width="85vw">
        <template v-slot:activator="{ on, attrs }">
            <v-btn
            fab 
            color="red"
            v-bind="attrs" 
            v-on="on"
            style="color: white;"
            @click="dialog = !dialog">
                <v-icon>mdi-chat-question</v-icon>
            </v-btn>
        </template>
        <v-card>
            <v-card-title>
                <h3>Reportar bug e sugestões</h3>
            </v-card-title>
            <v-card-text>
                <v-form v-model="valid" ref="form" lazy-validation>
                  <v-container>
                    <v-row dense>  
                      <v-col cols="12" md="12">
                        <v-textarea dense filled required v-model="form.bug" :rules="textRules" label="Insira uma descrição detalhada!" rows="5"></v-textarea>
                      </v-col>
                      <v-col align="center" cols="12" md="12" no-gutter>
                        <v-btn :disabled="!valid" color="success" class="mr-4" @click="addBug">Enviar</v-btn>
                        <v-btn color="warning" class="mr-4" @click="close">Cancelar</v-btn>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-form>
            </v-card-text>
        </v-card>
    </v-dialog>
</template>

<script>
export default {
    name: 'SuportStatus',
    data () {
        return {
            dialog: false,
            valid: true,
            ticketError: '',
            form: {
              company: null,
              company_worker: null,
              title: '',
              bug: '',
            },
            textRules: [
              v => !!v || 'Obrigatório',
              v => (v && v.length >= 10) || 'Ao menos 10 caracteres',
            ],
        }
    },
    mounted () {
    },
    methods: {
        close () {
           this.dialog = false
           this.reset()
        },
        reset () {
            this.$refs.form.reset()
        },
        validate () {
            this.$refs.form.validate()
        },
        async addReport () {
            this.validate()
            if (this.valid) {
                this.form.company = localStorage.getItem('company')
                this.form.company_worker = localStorage.getItem('user_id')
                const req = fetch(process.env.HOST_BACK+'/addBugReport/',{
                    method: 'POST',
                    body: JSON.stringify(this.form),
                    headers: {'Content-Type': 'application/json'},
                })
                const res = await req
                if (res.status == 200) {
                    this.reset()
                    this.$emit('getTickets')
                    this.dialog = false
                }else {
                    this.ticketError = "Erro ao registrar: " + res.status
                }
            }else {
            }
        },
    }
}
</script>
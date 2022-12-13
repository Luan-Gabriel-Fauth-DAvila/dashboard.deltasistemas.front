<template>
    <v-menu
        ref="menu"
        v-model="menu"
        :close-on-content-click="false"
        transition="scale-transition"
        offset-y
        min-width="auto">
        <template v-slot:activator="{ on, attrs }">
            <v-text-field 
                filled
                dense
                :dark="dark"
                v-model="date"
                :label="mainLabel"
                :prepend-inner-icon="prependIcon"
                readonly
                v-bind="attrs"
                v-on="on"
            ></v-text-field>
        </template>
        <v-date-picker
            v-model="date"
            :max="(new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10)"
            min="1950-01-01"
            @change="save"></v-date-picker>
    </v-menu>
</template>

<script>
export default {
    name: 'DateField',
    emits: ['changeEmit'],
    props: {
        mainLabel: {
            type: String,
            default: 'No Label'
        },
        dark: {
            type: Boolean,
            default: true
        },
        prependIcon: {
            type: String,
            default: 'mdi-calendar'
        }
    },
    data: () => ({
        date: null,
        menu: false,
    }),
    watch: {
        menu (val) {
            val && setTimeout(() => (this.activePicker = 'YEAR'))
        },
    },
    methods: {
        save (date) {
            this.$emit('changeEmit')
            this.$refs.menu.save(date)
        },
    },

}
</script>

<style>

</style>
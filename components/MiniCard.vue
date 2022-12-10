<template>
    <div>
        <v-card v-if="!loading" id="main" class="d-flex justify-space-between align-center" :style="{background: bgColor}">
            <span v-if="side == 'left'" :style="{color: txColor}" class="material-icons-outlined">{{ icon }}</span>
            <div>
                <label :style="{color: txColor}">{{ label }}</label>
                <h4 :style="{color: txColor}">{{ value }}</h4>
            </div>
            <span v-if="side == 'right'" :style="{color: txColor}" class="material-icons-outlined">{{ icon }}</span>
        </v-card>
        <v-card v-else :style="{background: bgColor}">
            <v-skeleton-loader max-height="12vh" width="18vw" type="card"></v-skeleton-loader>
        </v-card>
    </div>
    
</template>

<script>
export default {
    name: 'MiniCard',
    props: {
        label: {
            type: String,
            default: 'No Label'
        },
        value: {
            type: String,
            default: 'Carregando...'
        },
        txColor: {
            type: String,
            default: '#1b2559'
        },
        bgColor: {
            type: String,
            default: '#fff'
        },
        side: {
            type: String,
            default: 'right'
        },
        icon: {
            type: String,
            default: 'info'
        },
        loading: {
            type: Boolean,
            default: true
        }
    },
}
</script>

<style scoped>
#main {
    width: 18vw;
    padding: 3vh 5vh;
    color: white;
    z-index: 0;
    /* background: linear-gradient(135deg, rgb(67, 24, 255), rgb(134, 140, 255)); */
}
</style>
<template>
    <v-app>
        <Nuxt />
        <v-speed-dial
        class="v-btn-fab"
        :transition="'scale-transition'">
            <template v-slot:activator>
                <SuportStatus></SuportStatus>
            </template>
        </v-speed-dial>
    </v-app>
</template>

<script>
import SuportStatus from '../components/SuportStatus.vue';
export default {
    name: "DefaultLayout",
    components: {
        SuportStatus,
    }
}
</script>

<style>
.v-btn-fab {
    position: fixed;
    bottom: 0;
    right: 0;
    margin: 0 2vw 4vh 0;
}
</style>
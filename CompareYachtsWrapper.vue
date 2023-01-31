<template>
    <v-app>
        <v-main>
            <div v-if="isObjectEmpty(owner)">
                <v-skeleton-loader type="list-item"/>
            </div>
            <div v-if="this.collectedYachts.length > 0">
                <compare-yachts/>
            </div>
        </v-main>
    </v-app>
</template>

<script>
    import compareYachts from "./CompareYachts";
    import {mapState} from "vuex";

    export default {
        name: 'CompareYachtWrapper',
        props: ['cy'],
        components: {
            compareYachts
        },
        computed: {
            ...mapState({
                owner: (state) => state.domain.all,
                collectedYachts: (state) => state.search.item,
            }),
        },
        async mounted() {
            await this.$store.dispatch('search/load');

            let c = '';
            let list = this.cy.split(',');
            for (let i = 0; i < list.length; i ++) {
                c += '&yacht_id[]=' + list[i];
            }
            axios.get('/api/v1/search/yachts?r=1' + c)
                .then(res => {
                    this.$store.dispatch('search/collectYachts', res.data.data, {root: true});
                });
        },
    }
</script>
<style lang="scss" scoped>
    .v-application {
        background-color: white;
    }
</style>

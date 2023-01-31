<template>
    <div class="search-results" id="search-results-container">
        <v-skeleton-loader
            v-if="loading"
            class="mx-auto blue-grey lighten-2 pa-10 text-center app-loader"
            type="image"
        >
            <v-img :src="getPreloaderImgSrc()" height="145" contain />
            <span class="text-white"> LYW proceeds to your pre-selection directly with our partners yacht charter managers</span>
        </v-skeleton-loader>
        <v-container fluid v-else>
            <div v-if="collected.length" class="d-inline-block text-center compare-block shake-effect">
                <v-badge
                    color="primary"
                    overlap>
                    <span class="pulse"></span>
                    <template v-slot:badge>{{collectedLength}}</template>
                    <v-btn fab dark color="golden" class="rounded" @click="goToCompare"
                           :href="getYachtCompareHrefLink()">
                        <v-icon>mdi-select-compare</v-icon>
                    </v-btn>
                    <div class="font-weight-bold compare-text mt-3">Compare</div>
                </v-badge>
            </div>
            <v-row>
                <v-col cols="12" lg="12" class="mx-auto">
                    <h1 class="search-section-head primary--text font-weight-bold">
                        <span class="d-inline-block">
                        {{ filteredData.length }} {{ (filteredData.length === 1) ? 'YACHT' : 'YACHTS' }}
                        </span>
                        <span class="d-inline-block">
                            PRE-SELECTED
                        </span>
                    </h1>
                </v-col>
            </v-row>
            <search-filters :currency-filter="true" />
            <search-filters-mobile-dialog />

            <v-row v-if="filteredData.length > 0" align="center" justify="start">
                <search-result-card v-for="data in filteredData"
                                    :key="data.id"
                                    :item="data"
                                    :collected="collected"
                                    @removeFromCompare="removeYacht"
                                    @addToCompare="collectYachtsToCompare"
                                    @goToInquiry="inquireYachts"
                />
            </v-row>
            <v-row v-else class="my-12">
                <v-col class="text-center col-4 mx-auto">
                    <h3 class="font-weight-bold grey--text">No Yachts Available in the selected date range</h3>
                    <v-img :src="noYachtImage" width="80%" class="mx-auto my-8"></v-img>
                </v-col>
            </v-row>
        </v-container>
    </div>
</template>
<script>
import {mapState, mapGetters, mapActions} from 'vuex';
    import SearchResultCard from "./SearchResultCard";
    import SearchFilters from "../../../components/search/SearchFilters";
    import SearchFiltersMobileDialog from "../../../components/search/SearchFiltersMobileDialog";
    import preloaderImg from '../../assets/preloader.gif';

    export default {
        name: 'SearchResult',
        components: {
            SearchFiltersMobileDialog,
            SearchFilters,
            SearchResultCard
        },
        props: ['destinations', 'package', 'date_from', 'date_to', 'model'],
        data: () => ({
            data: [],
            collected: [],
            searchBarWatcher: null
        }),
        computed: {
            ...mapGetters({
                filteredYachts: 'filters/filteredYachts'
            }),
            ...mapState({
                owner: (state) => state.domain.all,
                isSearchBarLoading: (state) => state.searchBar.loading,
                selected: (state) => state.searchBar.selected,
                loading: (state) => state.search.loading,
                yachts: (state) => state.search.all,
                currency: (state) => state.currency
            }),
            collectedLength() {
                return this.collected.length;
            },
            filteredData() {
                return this.filteredYachts;
            }
        },
        methods: {
            ...mapActions({
                loadSearchResult: 'search/load'
            }),
            collectYachtsToCompare(data) {
                this.collected.push(data);
                data.selected = !data.selected;
            },
            removeYacht(data) {
                let index = this.collected.indexOf(data);
                this.collected.splice(index, 1);
                data.selected = !data.selected;
            },
            inquireYachts(id) {
                this.$router.push({ path: `/yachts/${id}` });
            },
            goToCompare() {
                const cy = _.map(this.collected, x => x.id);
                if (cy.length > 1) {
                    this.$router.push({path: `/yachts/compare/${cy}`})
                }
            },
            getYachtCompareHrefLink() {
                if (!!this.link) {
                    let c = '';
                    for (let i = 0; i < this.collected.length; i++) {
                        c += '&cy[]=' + this.collected[i].id;
                    }
                    return '/yachts/compare?r=1' + c;
                }
            },
            scrollToResults() {
                let top = ~~(document.getElementById('search-results-container').offsetTop);

                if (top <= 50) {
                    top = 0;
                } else {
                    top -= 50;
                }

                window.scroll({top: top, left: 0, behavior: 'smooth'});
            },
            getPreloaderImgSrc() {
                let ret = preloaderImg;
                if (this.isWidget === true && !_.isUndefined(process.env.MIX_STATIC_URL)) {
                    ret = process.env.MIX_STATIC_URL + ret;
                }
                return ret;
            },
            async loadYachts() {
                await this.loadSearchResult();
            }
        },
        async mounted() {
            await this.loadYachts();
        },
        watch: {
            currency: {
                deep: true,
                handler() {
                    this.loadYachts();
                }
            },
            loading(val) {
                if (val === true) {
                    this.scrollToResults();
                }
            }
        }
    }
</script>
<style lang="scss" scoped>
    @import 'SearchResult';
</style>

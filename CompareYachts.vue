<template>
    <div class="compare-yachts">
        <v-img :src="compareBg" class="text-white text-center compare-bg" height="59.132vh">
            <v-row class="pb-8" align="end" justify="center" style="height:100%"
                   :style="{background: compareBackgroundColor}">
                <v-col>
                    <v-row>
                        <v-col>
                            <h1 class="display-2 text-uppercase compare-head mb-12"
                                style="word-spacing:0.5em;font-size:48px">compare yacht models</h1>
                            <!--                                <h4 class="title">Get help choosing. <a href="#" class="font-weight-bold text-white">Contact-->
                            <!--                                    us</a></h4>-->
                        </v-col>
                    </v-row>
                    <v-container>
                        <v-row>
                            <v-col class="back-to-results mb-5 mx-auto primary--text" cols="11" lg="10" xl="10">
                                <v-btn color="primary" class="border-1" large tile @click="backToResults">
                                    <v-icon>mdi-chevron-left</v-icon>
                                    Back to listing
                                </v-btn>
                            </v-col>
                        </v-row>
                    </v-container>
                </v-col>
            </v-row>
        </v-img>
        <v-container>

            <v-row>
                <v-col cols="12" md="12" lg="10" class="mx-auto mobile-horizontal-scroll">
                    <v-row class="compare-section" >
                        <v-col cols="4" v-for="yacht in yachtsCollection" :key="yacht.id">
                            <v-select
                                :items="yachtsList(yacht.id)"
                                :value="yacht"
                                solo
                                flat
                                item-text="name"
                                item-value="name"
                                outlined
                                item-color="primary"
                                color="primary"
                                class="text-uppercase font-family-bold selector primary--text" return-object
                                @change="changeCompare(yacht.id,$event)"
                            >
                                <template v-slot:selection="data">
                                    {{ data.item.name }}
                                    <!--                                        <template v-if="typeof data.item !== 'object'">-->
                                    <!--                                            <v-list-item-content-->
                                    <!--                                                v-text="data.item"></v-list-item-content>-->
                                    <!--                                        </template>-->
                                    <!--                                        <template>-->
                                    <!--                                            <v-list-item-content>-->
                                    <!--                                                <v-list-item-title>-->
                                    <!--                                                    <span v-if="data.item.id !== collectedYacht.id">{{data.item.name}}</span>-->
                                    <!--                                                </v-list-item-title>-->
                                    <!--                                            </v-list-item-content>-->
                                    <!--                                        </template>-->
                                </template>
                            </v-select>
                            <v-card class="compare-card" elevation="1">
                                <v-img class="yacht-background-image" :src="mainImage(yacht)" height="200px">
                                    <div class="yacht-background-image-gradient"
                                         :style="{background: backgroundGradient}"></div>
                                </v-img>
                                <div class="pa-8">
                                    <v-row>
                                        <v-col class="text-center mx-auto" cols="6">
                                            <!-- @Todo Book now integration-->
                                            <v-btn color="primary" tile large block @click="inquireYachts(yacht)">
                                                MORE INFO
                                            </v-btn>
                                        </v-col>
                                    </v-row>
                                    <v-row>
                                        <v-col>
                                            <h4 class="title font-family-bold text-black text-uppercase">
                                                Rates</h4>
                                            <h4 class="subtitle-1 mt-2">From</h4>
                                            <h4><span
                                                class="font-weight-bold text-black title">{{startFrom(yacht)}}</span>
                                                per day
                                            </h4>
                                            <h4 class="my-3">{{yacht.marina.name}}</h4>
                                        </v-col>
                                    </v-row>
                                    <v-divider class="mb-5"></v-divider>
                                    <v-row>
                                        <v-col>
                                            <h4 class="subtitle-1 font-family-bold text-black text-uppercase mb-5">
                                                Specifications
                                            </h4>
                                            <p>Type: {{yacht.type.name}}</p>
                                            <p>Guests: {{getMax(yacht)}}</p>
                                            <p>Cabins: {{yacht.rooms.length}}</p>
                                            <p>Length: {{yacht.length_overall_m}} m</p>
                                            <p>Beam: {{yacht.beam_m}} m</p>
                                            <p>Draft: {{yacht.draft_m}} m</p>
                                            <p>Built: {{yacht.build_year}}</p>
                                            <p>Cruising speed: {{yacht.cruising_speed_kts}} knots</p>
                                        </v-col>
                                    </v-row>
                                    <v-divider class="mb-5"></v-divider>
                                    <v-row>
                                        <v-col>
                                            <h4 class="subtitle-1  font-family-bold text-black text-uppercase">
                                                activities
                                            </h4>
                                            <v-row>
                                                <v-col class="activity-list"
                                                       cols="12">
                                                    <!--                                                        <h4 class="subtitle-2 mb-3">Trip Type : {{day.name}}</h4>-->
                                                    <ul v-if="activities(yacht).length > 0">
                                                        <li v-for="activity in activities(yacht)" :key="activity.name">
                                                            {{activity.name}}
                                                        </li>
                                                    </ul>
                                                    <p v-else>No Activities</p>
                                                </v-col>
                                            </v-row>
                                        </v-col>
                                    </v-row>
                                    <v-divider class="mb-5"></v-divider>
                                    <v-row>
                                        <v-col>
                                            <h4 class="subtitle-1  font-family-bold text-black text-uppercase mb-3">
                                                Included Services
                                            </h4>
                                            <v-row>
                                                <v-col class="activity-list" cols="12">
                                                    <!--                                                        <h4 class="subtitle-2 mb-3">Trip Type : {{day.name}}</h4>-->
                                                    <ul v-if="services(yacht).length > 0">
                                                        <li v-for="service in services(yacht)">{{service.name}}
                                                        </li>
                                                    </ul>
                                                    <p v-else>No Services</p>
                                                </v-col>
                                            </v-row>
                                            <p class="my-5">More services available on request</p>
                                        </v-col>
                                    </v-row>
                                    <v-row>
                                        <v-col class="text-center mx-auto" cols="6">
                                            <!-- @Todo Book now integration-->
                                            <v-btn color="primary" tile large block @click="inquireYachts(yacht)">
                                                MORE INFO
                                            </v-btn>
                                        </v-col>
                                    </v-row>
                                </div>
                            </v-card>
                        </v-col>
                    </v-row>
                </v-col>
            </v-row>
        </v-container>
        <!--        <customFooter/>-->
        <!--        <scrollTop/>-->
    </div>
</template>

<script>
    //import customFooter from './Footer';
    //import scrollTop from './ScrollTop';
    import {mapState,mapGetters} from 'vuex'

    export default {
        name: "CompareYachts",
        components: {
            //customFooter,
            //scrollTop,
        },
        data: () => ({
            compareBg: 'https://ava-storage.s3-ap-southeast-1.amazonaws.com/images/background/search-compare.jpg',
            selectedYacht: {},
            yachtId: '',
        }),
        computed: {
            ...mapState({
                owner: (state) => state.domain.all,
                collectedYachts: (state) => state.search.item,
                listOfYachts: (state) => state.search.all,
                currency: (state) => state.currency.currency,
                selected: (state) => state.searchBar.selected
            }),
            ...mapGetters({
                backLink: 'search/backLink',
            }),
            yachtsCollection() {
                let changed = this.selectedYacht;
                let id = this.yachtId;
                if (!this.isObjectEmpty(this.selectedYacht)) {
                    _.remove(this.collectedYachts, function (yacht) {
                        return yacht.id === id;
                    });
                    this.collectedYachts.push(changed);
                    return _.sortBy(this.collectedYachts, function (yacht) {
                        return yacht.name;
                    });
                } else {
                    return _.sortBy(this.collectedYachts, function (yacht) {
                        return yacht.name;
                    });
                }
            },
            compareBackgroundColor() {
                let hex = this.owner && this.owner.sub_domain ? this.owner.sub_domain.main_color : '#aaaaaa';
                let rgb = ['0x' + hex[1] + hex[2] | 0, '0x' + hex[3] + hex[4] | 0, '0x' + hex[5] + hex[6] | 0].join(',');

                return `rgba(${rgb}, 0.3)`;
            },
            backgroundGradient() {
                let hex = this.owner && this.owner.sub_domain ? this.owner.sub_domain.main_color : '#aaaaaa';
                let rgb = ['0x' + hex[1] + hex[2] | 0, '0x' + hex[3] + hex[4] | 0, '0x' + hex[5] + hex[6] | 0].join(',');

                return `linear-gradient(0deg, rgba(${rgb}, 0.9), transparent)`;
            }
        },
        methods: {
            activities(yacht) {
                let list = []
                if (_.has(yacht, 'day_trip') && yacht.day_trip.sub_days.length > 0) {
                    yacht.day_trip.sub_days.forEach((day) => {
                        if (day.pivot.activities.length > 0) {
                            day.pivot.activities.forEach((activity) => {
                                list.push(activity)
                            })
                        }
                    })
                }
                return list;
            },
            services(yacht) {
                let list = []
                if (_.has(yacht, 'day_trip') && yacht.day_trip.sub_days.length > 0) {
                    yacht.day_trip.sub_days.forEach((day) => {
                        if (day.pivot.includes.length > 0) {
                            day.pivot.includes.forEach((service) => {
                                list.push(service)
                            })
                        }
                    })
                }
                return list;
            },
            mainImage: function (yacht) {
                let url = '';
                yacht.images.forEach((image) => {
                    if (image.main) {
                        url = image.src;
                    }
                })
                if (url === '') {
                    return this.defaultYachtBackgroundImage;
                } else {
                    return url;
                }
            },
            inquireYachts(data) {
                this.$store.dispatch('search/setBackLink', {compare: this.$route.path}).then(() => {
                    this.$store.dispatch('search/collectYachts', []);
                    this.$router.push({ path: `/yachts/${data.id}` });
                });
            },
            yachtsList: function (yachtId) {
                let yachtsList = _.cloneDeep(this.listOfYachts);
                this.collectedYachts.forEach((collectedYacht) => {
                    if (collectedYacht.id !== yachtId) {
                        _.remove(yachtsList, function (yacht) {
                            return yacht.id === collectedYacht.id;
                        });
                    }
                });
                return yachtsList;
            },
            changeCompare(id, e) {
                this.selectedYacht = e;
                this.yachtId = id;
                return this.selectedYacht;
            },

            backToResults() {
                this.$store.dispatch('search/collectYachts', []).then(() => {
                    this.$store.dispatch('search/setLoading', true);
                    this.$router.push(!_.isEmpty(this.backLink.search) ? this.backLink.search : '/');
                });
            }
        }
    }
</script>

<style lang="scss" scoped>
    .v-application, * {
        font-family: "Avenir Next", sans-serif !important;
    }

    .font-family-bold, .v-application .font-family-bold {
        font-family: "Avenir Next Bold", sans-serif !important;
    }
    .compare-head{
        text-shadow: 1px 1px 0 #ffffff;
    }
    .v-application p {
        margin-bottom: 10px;
    }

    .activity-list ul {
        column-count: 2;
        column-width: auto;
    }

    .yacht-background-image {
        .yacht-background-image-gradient {
            width: 100%;
            position: absolute;
            bottom: 0;
            height: 45%;
        }
    }
    .back-to-results{
        text-align: left;
    }

    @media (max-width: 600px) {
        .mobile-horizontal-scroll {
            overflow-x: auto;
            overflow-y: hidden;
        }
        .compare-section {
            width: 865px;
        }
        .compare-yachts .theme--light.v-select .v-select__selections {
            font-size: 15px !important;
        }
        .back-to-results {
            text-align: center !important;
        }
        .yacht-background-image {
            height: 20vh !important;
        }
    }
</style>

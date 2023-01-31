<template>
    <v-col cols="12" sm="6" lg="4" class="top-yacht" :data-cy="'yacht'+yacht.id">
        <v-hover v-slot:default="{ hover }">
            <v-card>
                <v-img v-if="!selectedToCompare" class="yacht-background-image"
                       :data-id="'selected'+yacht.id"
                       :src="mainImage(yacht)"
                       :aspect-ratio="aspectRatio"
                       height="320px">
                    <v-row align="end" class="lightbox white--text pa-md-2 fill-height text-center"
                           :style="{background: backgroundGradient}">
                        <v-col class="pt-md-3 pt-0" :hidden="hover">
                            <h2 class="title text-uppercase mb-3 font-family-bold">
                                {{yacht.name}}
                            </h2>
                            <div class="body-1">
                                <span>{{yacht.length_overall_m}} M</span>
                                <span v-if="!filters.package.id || filters.package.id && filters.package.id === OVERNIGHT_TRIP">| {{yacht.rooms.length}} Cabins</span>
                                <span>| {{getMax(yacht, filters.package.id)}} Guests <span v-if="filters.package.id && filters.package.id !== OVERNIGHT_TRIP">/ Day</span>
                                    <span v-if="filters.package.id && filters.package.id === OVERNIGHT_TRIP">/ Night</span>
                                </span>
                            </div>
                        </v-col>
                    </v-row>
                    <v-overlay
                        absolute
                        :value="hover"
                        :opacity="0.8"
                        color="#fff"
                    >
                        <v-menu
                            :close-on-content-click="false"
                            :nudge-width="100"
                            offset-y
                            offset-x
                            left
                            open-on-hover class="pin">
                            <template v-slot:activator="{ on }">
                                <v-btn v-on="on" class="pin float-right text-grey" fab dark small color="primary">
                                    <v-icon dark>mdi-map-marker</v-icon>
                                </v-btn>
                            </template>
                            <v-card class="text-center white">
                                <v-card-text>
                                    <h4>Provided By</h4>
                                    <v-img :src="yacht.central_agent.person.company_logo" contain
                                           class="image my-3 mx-auto"
                                           max-height="35px" max-width="100px"/>
                                    <h4>Destination</h4>
                                    <span>{{yacht.marina.name}}</span>
                                </v-card-text>
                            </v-card>
                        </v-menu>
                        <v-row align="center" justify="center" class="pa-md-2 fill-height text-center text-grey">
                            <v-col cols="12" class="pt-md-3 pt-0">
                                <h2 class="display-1 text-uppercase mb-5 hover-head font-weight-bold primary--text"
                                    @click="inquireYachts(yacht.id)" :href="getHrefLink(yacht.id)">
                                    {{yacht.name}}
                                </h2>
                                <div class="body-1 mb-3 text-grey">{{yacht.type.name}}</div>
                                <div class="body-1 mb-3">
                                    <span>{{yacht.length_overall_m}} M</span>
                                    <span v-if="!filters.package.id || filters.package.id && filters.package.id === OVERNIGHT_TRIP">| {{yacht.rooms.length}} Cabins</span>
                                    <span>| {{getMax(yacht, filters.package.id)}} Guests <span v-if="filters.package.id && filters.package.id !== OVERNIGHT_TRIP">/ Day</span>
                                        <span v-if="filters.package.id && filters.package.id === OVERNIGHT_TRIP">/ Night</span></span>
                                </div>
                                <div class="body-1">
                                    Start from
                                    <span class="primary--text font-weight-cold">
                                        {{ startFrom(yacht) }}
                                    </span>
                                    <span v-if="!filters.package.id || filters.package.id && filters.package.id !== OVERNIGHT_TRIP">per day</span>
                                </div>
                            </v-col>
                            <div class="primary--text">
                                <v-btn
                                    :data-cy="'compare'+yacht.id"
                                    v-if="!selectedToCompare"
                                    :outlined="outlined"
                                    @mouseover="outlined=false"
                                    @mouseleave="outlined=true"
                                    color="primary"
                                    @click="collectYachtsToCompare(yacht)"
                                    class="mr-3" small tile>
                                    Compare
                                </v-btn>
                                <v-btn
                                    v-else
                                    outlined
                                    color="error"
                                    @click="removeYacht(yacht)" class="mr-3" small tile>
                                    Remove
                                </v-btn>
                                <v-btn
                                    :data-cy="`inquire${yacht.id}` "
                                    color="primary"
                                    @click="inquireYachts(yacht.id)" class="ma-0 border-1" small tile
                                    :href="getHrefLink(yacht.id)">
                                    MORE INFO
                                </v-btn>
                            </div>
                            <v-col cols="12" class="gc-col">
                                <v-btn text color="primary" small @click="openDialog()">General conditions</v-btn>
                            </v-col>
                        </v-row>
                    </v-overlay>
                </v-img>
                <div
                    v-else
                    :data-id="`selected'${yacht.id}`"
                    class="d-flex white darken-2 display-3 result-overlay" data-cy="selected-yacht"
                    style="height: 330px">
                    <v-menu
                        :close-on-content-click="false"
                        :nudge-width="100"
                        offset-y
                        offset-x
                        left
                        open-on-hover class="pin">
                        <template v-slot:activator="{ on }">
                            <v-btn class="pin float-right" v-on="on" fab dark small color="primary">
                                <v-icon dark>mdi-map-marker</v-icon>
                            </v-btn>
                        </template>

                        <v-card class="text-center white">
                            <v-card-text>
                                <h4>Provided By</h4>

                                <v-img :src="yacht.central_agent.person.company_logo" contain
                                       class="image my-3 mx-auto"
                                       alt="Logo" height="35px" width="35px"/>
                                <h4>Destination</h4>
                                <p>{{yacht.marina.name}}</p>
                            </v-card-text>
                        </v-card>
                    </v-menu>
                    <v-row align="center" justify="center" class="text-center">
                        <v-col cols="12">
                            <h2 class="display-1 text-uppercase mb-5 hover-head font-weight-bold primary--text"
                                @click="inquireYachts(yacht.id)" :href="getHrefLink(yacht.id)">
                                {{yacht.name}}</h2>
                            <div class="body-1 mb-3 text-black">{{yacht.type.name}}</div>
                            <div class="body-1 mb-3">
                                <span>{{yacht.length_overall_m}} M</span>
                                <span v-if="!filters.package.id || filters.package.id && filters.package.id === OVERNIGHT_TRIP">| {{yacht.rooms.length}}Cabins</span>
                                <span>| {{getMax(yacht, filters.package.id)}} Guests <span v-if="filters.package.id && filters.package.id !== OVERNIGHT_TRIP">/ Day</span><span v-if="filters.package.id && filters.package.id === OVERNIGHT_TRIP">/ Night</span></span>
                            </div>
                            <div class="body-1">
                                Start from
                                <span class="primary--text font-weight-bold">
                                    {{ startFrom(yacht) }}
                                </span>
                                <span v-if="!filters.package.id || filters.package.id && filters.package.id !== OVERNIGHT_TRIP">per day</span>
                            </div>
                        </v-col>
                        <div class="primary--text">
                            <v-btn
                                :data-cy="'compare'+yacht.id"
                                v-if="!selectedToCompare"
                                :outlined="outlined"
                                @mouseover="outlined=false"
                                @mouseleave="outlined=true"
                                color="primary"
                                @click="collectYachtsToCompare(yacht)"
                                class="mr-3" small tile>
                                Compare
                            </v-btn>
                            <v-btn
                                v-else
                                outlined
                                color="error"
                                :data-cy="'remove'+yacht.id"
                                @click="removeYacht(yacht)" class="mr-3" small tile>
                                Remove
                            </v-btn>
                            <v-btn
                                :data-cy="`inquire'${yacht.id}`"
                                color="primary"
                                @click="inquireYachts(yacht.id)" class="ma-0 border-1" small tile
                                :href="getHrefLink(yacht.id)">
                                MORE INFO
                            </v-btn>
                        </div>
                        <v-col cols="12">
                            <v-btn text color="primary" small tile @click="openDialog()">General conditions</v-btn>
                        </v-col>
                    </v-row>
                </div>
            </v-card>
        </v-hover>
        <GeneralConditionsLanding v-model="dialog" :text="yacht.general_conditions"/>
    </v-col>
</template>

<script>
    import GeneralConditionsLanding from "../GeneralConditions/GeneralConditions";
    import {mapState} from "vuex";

    export default {
        name: "SearchResultCard",
        props: ['item', 'collected'],
        components: {GeneralConditionsLanding},
        data: () => ({
            aspectRatio: 16 / 9,
            outlined: true,
            dialog: false,
            selectedToCompare: false,
            hover: false
        }),
        computed: {
            ...mapState({
                owner: (state) => state.domain.all,
                filters: (state) => state.searchBar.selected,
                currency: (state) => state.domain.currency.code
            }),
            yacht() {
                return this.item;
            },
            backgroundGradient() {
                let hex = this.owner && this.owner.sub_domain ? this.owner.sub_domain.main_color : '#aaaaaa';
                let rgb = ['0x' + hex[1] + hex[2] | 0, '0x' + hex[3] + hex[4] | 0, '0x' + hex[5] + hex[6] | 0].join(',');

                return `linear-gradient(0deg, rgba(${rgb}, 0.9), transparent)`;
            }
        },
        methods: {
            mainImage: function (yacht) {
                let url = this.defaultYachtBackgroundImage;
                let main = 0;
                yacht.images.forEach((image) => {
                    if (image.main) {
                        url = image.src;
                        main = 1;
                    }
                });
                if (!main && yacht.images.length > 0) {
                    url = yacht.images[0].src;
                }
                return url;
            },
            checkUrlParameter(string) {
                return !/\?/.test(string) ? string.replace(/&date_from/, '?date_from') : string;
            },
            getHrefLink(id) {
                if (!!this.link) {
                    //return '/yachts/' + id + '?rb=' + encodeURIComponent(this.checkUrlParameter(window.location.href + '&date_from=' + this.dateFrom + '&date_to=' + this.dateTo));
                    //return '/yachts/' + id + '?rb=' + encodeURIComponent(window.location.href) + '&date_from=' + this.dateFrom + '&date_to=' + this.dateTo;
                    return `/${id}`
                }
            },
            inquireYachts(data) {
                this.$emit('goToInquiry', data)
            },
            removeYacht(data) {
                this.$emit('removeFromCompare', data);
            },
            collectYachtsToCompare(data) {
                this.$emit('addToCompare', data);
            },
            openDialog() {
                this.dialog = true;
            }
        },
        watch: {
            collected(newVal, oldVal) {
                this.selectedToCompare = false;
                if (newVal.length) {
                    for (let i = 0; i < newVal.length; i++) {
                        if (newVal[i].id === this.item.id) {
                            this.selectedToCompare = true;
                        }
                    }
                }
            }
        }
    }
</script>

<style scoped lang="scss">
    .yacht-background-image {
        .v-responsive__content {
            display: flex !important;
            align-items: flex-end !important;
        }

        border-radius: 4px !important;
    }

</style>

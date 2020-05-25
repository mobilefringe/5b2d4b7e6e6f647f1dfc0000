<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loading-spinner v-if="!dataLoaded"></loading-spinner>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak>
                <div class="inside_page_header" v-if="pageBanner" v-bind:style="{ backgroundImage: 'url(' + pageBanner.image_url + ')' }">
                    <div class="main_container position_relative">
                        <h1>Newsletter</h1>
                    </div>
                </div>
                <div class="main_container">
                    <div class="row">
                        <div class="col-md-12">
                            <breadcrumb></breadcrumb>
                            <div v-if="pageContent" v-html="pageContent.body"></div>
                            <!--<form class="newsletter_form form-horizontal" action="//mobilefringe.createsend.com/t/d/s/vjklyu/" method="post">-->
                            <form class="js-cm-form" id="subForm" action="https://www.createsend.com/t/subscribeerror?description=" method="post" data-id="92D4C54F0FEC16E5ADC2B1904DE9ED1A2FD19F3F7203DAD4A4A3E5920220527BCBE295883C7565A1B61778914F9F661C4DD25C891DE330C54B1FBB92CE4A98A6">
                                <div class="row">
                                    <div class="col-sm-6" >
                                        <label for="fieldyhukldd" class="visuallyhidden">First Name</label>
                                        <input v-model="form_data.first_name" required class="margin_20 form-control" aria-label="First Name" id="fieldyhukldd" name="cm-f-yhukldd" type="text" placeholder="First Name">
                                    </div>
                                    <div class="col-sm-6" >
                                        <label for="fieldyhukldh" class="visuallyhidden">Last Name</label>
                                        <input v-model="form_data.last_name" required class="margin_20 form-control" aria-label="Last Name" id="fieldyhukldh" name="cm-f-yhukldh" type="text" placeholder="Last Name">
                                    </div>
                                </div>
                                <div class="row">
                                    <div class="col-sm-12">
                                        <label for="fieldEmail" class="visuallyhidden">Email</label>
                                        <input v-model="form_data.email" required class="margin_20 form-control" name="cm-ykykad-ykykad" type="email" placeholder="Email" id="fieldEmail" aria-label="Email">
                                    </div>
                                </div>
                                <div class="row">
                                    <div class="col-sm-12">
                                        <div style="margin-left: 20px">
                                            <label class="checkbox">
                                                <input name="agree_newsletter" required  type="checkbox" id="cm-privacy-consent">
                                                I agree to receive communications from {{ property.name }}.
                                            </label>
                                        </div>
            					    </div>
            					</div>
        					    <div class="margin_40 clearfix"></div>
        					    <div class="row">
                                    <div class="col-xs-12">
                                        <button class="animated_btn" type="submit" :disabled="formSuccess">Subscribe</button>
                                    </div>
                                </div>
                            </form> 
                        </div>
                    </div>
                </div>
            </div>
        </transition>
    </div>
</template>
<script>
    define(["Vue", "vuex", "jquery", "vee-validate", "json!site.json", "campaignMonitor"], function(Vue, Vuex, $, VeeValidate, site, campaignMonitor) {
        Vue.use(VeeValidate);
        return Vue.component("newsletter-component", {
            template: template, // the variable template will be injected
            data: function() {
                return {
                    dataLoaded: true,
                    pageBanner: null,
                    pageContent: null,
                    siteInfo: site,
                    form_data : {},
                    formSuccess : false,
                    formError: false
                }
            },
            mounted () {
                this.form_data.first_name = this.$route.query.name;
                $("#fieldfihudt").val(this.form_data.first_name);
                this.form_data.email = this.$route.query.email;
                $("#newsletter_email").val(this.form_data.email);
            },
            watch : {
                $route () {
                    this.form_data.first_name = this.$route.query.name;
                    $("#fieldfihudt").val(this.form_data.first_name);
                    this.form_data.email = this.$route.query.email;
                    $("#newsletter_email").val(this.form_data.email);
                }
            },
            created() {
                this.loadData().then(response => {
                    var temp_repo = this.findRepoByName('Newsletter Banner').images;
                    if(temp_repo != null) {
                        this.pageBanner = temp_repo[0];
                    } else {
                        this.pageBanner = {
                            "image_url": "//codecloud.cdn.speedyrails.net/sites/5de0705d6e6f6416c4090000/image/jpeg/1529532304000/insidebanner2.jpg"
                        }
                    }
                    
                    this.pageContent = response[0].data;
                    this.dataLoaded = true;
                });
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'findRepoByName'
                ])
            },
            methods: {
                loadData: async function () {
                    this.property.mm_host = this.property.mm_host.replace("http:", "");
                    try {
                        let results = await Promise.all([this.$store.dispatch('LOAD_PAGE_DATA', {url: this.property.mm_host + "/pages/cerritos-newsletter.json"}), this.$store.dispatch("getData", "repos")]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                }
            }
        });
    });
</script>
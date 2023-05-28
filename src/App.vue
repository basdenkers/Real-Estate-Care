<template>
    <link rel="stylesheet" v-bind:href="cssBase">
    <link rel="stylesheet" v-bind:href="cssFile">

    <app-header />
    <router-view></router-view>
    <app-footer />
    <NoWifiModal />

    <div v-if="showOfflineToast" class="toast-container">
        <div v-bind:class="{'show': showOfflineToast}" :class="'toast toast-' + toastOfflineType">
            {{ toastOfflineMessage }}
        </div>
    </div>
</template>


<script>
    import AppHeader from '@/components/template/AppHeader.vue'
    import AppFooter from '@/components/template/AppFooter.vue'
    import NoWifiModal from '@/components/alerts/WifiCheck.vue'
    import store from './store/store.js'
    import axios from 'axios'

    import baseStyle from '@/assets/css/base.css?url'
    import mainStyle from '@/assets/css/main.css?url'
    import darkStyle from '@/assets/css/dark.css?url'

    export default {
        components: {
            AppHeader,
            AppFooter,
            NoWifiModal
        },
        created() {
            this.saveOfflineInspections();
        },  
        mounted() {
            window.addEventListener('online', this.saveOfflineInspections);
        },
        data() {
            return {
                showOfflineToast: false,
                toastOfflineMessage: '',
                toastOfflineType: '',
            }
        },  
        methods: {
            async saveOfflineInspections() {
                if (store.getters.offlineInspections && store.getters.offlineInspections.length > 0) {
                    try {
                        const response = await axios.get(`https://api.jsonbin.io/v3/b/63c1a09815ab31599e35cf00/latest`, {
                            headers: {
                                'Content-Type': 'application/json',
                                'X-Master-Key': '$2b$10$6OQ5plkCt1vMLN8m7VMniOP5RSMQB3WOfPoQlYh/JNbs2xeF7psUu'
                            }
                        });

                        store.getters.offlineInspections.forEach(inspection => {
                            if (inspection.addressId === null || inspection.addressId === undefined || inspection.inspectionId === null || inspection.inspectionId === undefined) return;
                            const addressIndex = response.data.record.addresses.findIndex(address => address.id === inspection.addressId);
                            const inspectionIndex = response.data.record.addresses[addressIndex].inspections.findIndex(insp => insp.id === inspection.inspectionId);
                            if (response.data.record.addresses[addressIndex] === null || response.data.record.addresses[addressIndex] === undefined ||
                                response.data.record.addresses[addressIndex].inspections === null || response.data.record.addresses[addressIndex].inspections === undefined ||
                                response.data.record.addresses[addressIndex].inspections[inspectionIndex] === null || response.data.record.addresses[addressIndex].inspections[inspectionIndex] === undefined ) return;

                            response.data.record.addresses[addressIndex].inspections[inspectionIndex].completion = inspection.completion;
                            response.data.record.addresses[addressIndex].inspections[inspectionIndex].damage = inspection.damage;
                            response.data.record.addresses[addressIndex].inspections[inspectionIndex].maintenance = inspection.maintenance;
                            response.data.record.addresses[addressIndex].inspections[inspectionIndex].installations = inspection.installations;
                            response.data.record.addresses[addressIndex].inspections[inspectionIndex].modifications = inspection.modifications;
                            response.data.record.addresses[addressIndex].inspections[inspectionIndex].cleanlinessScore = inspection.cleanlinessScore;
                            response.data.record.addresses[addressIndex].inspections[inspectionIndex].somethingBroken = inspection.somethingBroken;
                            delete inspection.addressId;
                            delete inspection.inspectionId;
                        });

                        const update = await axios.put(`https://api.jsonbin.io/v3/b/647100d39d312622a365ef81`, response.data.record, {
                            headers: {
                                'Content-Type': 'application/json',
                                'X-Master-Key': '$2b$10$vOp9rOZvWQmkRQQgx3dv/OpIWEBqJAyumCENg6eObsE645/h0Mupq'
                            }
                        });

                        this.showOfflineToast = true;
                        this.toastOfflineMessage = 'Offline inspections saved successfully';
                        this.toastOfflineType = 'success';

                        store.commit("setOfflineInspections", []);

                        setTimeout(() => {
                            this.toastType = '';
                            this.toastMessage = '';
                            this.showToast = false;
                        }, 3000);
                    } catch (error) {

                        this.showOfflineToast = true;
                        this.toastOfflineMessage = 'Error saving offline inspections';
                        this.toastOfflineType = 'error';

                        setTimeout(() => {
                            this.toastType = '';
                            this.toastMessage = '';
                            this.showToast = false;
                      }, 3000);
                    }
                }
            },
        },
        computed: {
            cssBase() {
                return baseStyle;
            },
            cssFile() {
                return store.getters.darkTheme ? darkStyle : mainStyle;
            }
        }
    }
</script>
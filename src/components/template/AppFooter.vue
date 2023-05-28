<script setup>
import { searchCircle, helpCircle, arrowRedoCircle} from 'ionicons/icons';
</script>
<template>
    <footer class="container-fluid bg-secondary position-fixed bottom-0" role="navigation" aria-label="footer navigatie">
        <div class="row p-2">

            <div class="col d-flex flex-column justify-content-center text-center">
                <router-link v-if="loggedIn" to="/" aria-label="Active task" aria-labelledby="footer-build-icon" class="d-flex flex-column align-items-center justify-content-center">
                    <ion-icon class="fs-1 m-auto" id="footer-arrow-icon" :icon="arrowRedoCircle"></ion-icon>
                    <span class="small">Active task</span>
                </router-link>
            </div>

            <div class="col d-flex flex-column justify-content-center text-center">
                <router-link v-if="loggedIn" to="/search" aria-label="Search" aria-labelledby="footer-search-icon" class="d-flex flex-column align-items-center justify-content-center">
                    <ion-icon class="fs-1 m-auto" id="footer-search-icon" :icon="searchCircle"></ion-icon>
                    <span class="small">Search</span>
                </router-link>
            </div>

            <div class="col d-flex flex-column justify-content-center text-center">
                <router-link v-if="loggedIn" to="/help" aria-label="Help" aria-labelledby="footer-alertCircle-icon" class="d-flex flex-column align-items-center justify-content-center">
                    <ion-icon class="fs-1 m-auto" id="footer-alertCircle-icon" :icon="helpCircle"></ion-icon>
                    <span class="small">Help</span>
                </router-link>
            </div>

        </div>
    </footer>
</template>

<script>
    import store from '../../store/store.js'

    export default {
        data() {
            return {
                loggedIn: false,
            }
        },
        created(){
            if (store.getters.userID) {
                this.loggedIn = true;
            }
        },
        watch: {
            '$store.getters.userID': {
                handler(newValue) {
                    this.loggedIn = newValue;
                },
                deep: true
            }
        },
        mounted() {
            this.$store.watch(
                (state) => (state.getters != undefined && state.getters.userID != undefined) ? state.getters.userID : false,
                (newValue) => {
                    this.loggedIn = newValue;
                }
            );
        }
    }
</script>
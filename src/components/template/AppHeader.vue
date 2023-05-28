<script setup>
    import { cogOutline, notificationsCircle, notificationsOffCircle } from 'ionicons/icons';


</script>

<template>
    <header class="bg-primary container-fluid">
        <div class="row text-light p-3">
            <div class="col">
                <router-link :to="{ name: 'home' }" aria-label="Go Back">
                    <img alt="RealEstateCare logo" class="logo" src="../../assets/realestatecare/logo.svg" height="150" />
                </router-link>
            </div>
            <div class="col d-flex justify-content-end gap-2 align-items-center">
                <nav>
                    <ul class="list-unstyled m-0">
                        <li class="d-flex gap-3">
                            <ion-icon v-if="notificationsEnabled" class="fs-1" id="notifications-icon" :icon="notificationsCircle" aria-label="notifications icon"></ion-icon>
                            <ion-icon v-else class="fs-1" id="notifications-off-icon" :icon="notificationsOffCircle" aria-label="notifications off icon"></ion-icon>
                            <router-link v-if="loggedIn" to="/settings" aria-label="settings"><ion-icon class="fs-1" id="settings-icon" :icon="cogOutline"></ion-icon></router-link>
                        </li>
                    </ul>
                </nav>
            </div>
        </div>
    </header>
</template>

<script>
    import store from '../../store/store.js'

    export default {
        data() {
            return {
                loggedIn: false,
                notificationsEnabled: false,
            }
        },
        created() {
            if (store.getters.userID) {
                this.loggedIn = true;
                this.notificationsEnabled = store.getters.userNotifications;
            }
        },
        watch: {
            '$store.getters.userNotifications': {
                handler(newValue) {
                    this.notificationsEnabled = newValue;
                },
                deep: true
            }
        },
        mounted() {
            this.$store.watch(
                (state) => (state.getters != undefined && state.getters.userNotifications != undefined) ? state.getters.userNotifications : false,
                (newValue) => {
                    this.notificationsEnabled = newValue;
                }
            );
        }
    }
</script>
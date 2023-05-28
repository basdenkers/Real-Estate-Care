<template>
    <main class="container">
        <router-link :to="{ name: archive }" class="btn btn-primary mb-4" aria-label="Go back">Go back</router-link>
        <h1>Inspections {{address}}</h1>
        <div class="list-group gap-3">
            <div class="list-group-item list-group-item-action d-flex justify-content-between align-items-center" v-for="inspection in inspections" :key="inspection.id">
                <div>
                    <p class="mb-0">Inspection ID: {{ inspection.id }}</p>
                    <p class="mb-0">Date: {{ inspection.date }}</p>
                    <div class="badge" :class="{'text-bg-success': inspection.completion, 'text-bg-danger': !inspection.completion }" aria-label="Inspection status">{{ inspection.completion ? 'Completed' : 'In progress' }}</div>
                </div>
                <router-link :to="{ name: 'inspection' }" @click="saveParams(inspection.id, addressId)" class="btn btn-primary" aria-label="Go to Inspection">Go to Inspection</router-link>
            </div>
        </div>
    </main>
</template>

<script>
    import axios from 'axios';
    import store from '../store/store.js'

    export default {
        name: 'Inspections',
        data() {
            return {
                inspections: [],
                addressId: '',
                address: '',
                archive: 'scheduled',
            };
        },
        methods: {
            saveParams(inspection, address) {
                store.commit("changeInspection", inspection);
                store.commit("changeAddress", address);
            }
        },
        async created() {
            this.addressId = store.getters.address;
            this.archive = (store.getters.completion) ? 'completed' : 'scheduled';
            
            try {
                const response = await axios.get(`https://api.jsonbin.io/v3/b/63c1a09815ab31599e35cf00/latest`, {
                    headers: {
                        'Content-Type': 'application/json',
                        'X-Master-Key': '$2b$10$6OQ5plkCt1vMLN8m7VMniOP5RSMQB3WOfPoQlYh/JNbs2xeF7psUu'
                    }
                });

                let address = response.data.record.addresses.filter(address => address.id === this.addressId);
                let inspections = address[0].inspections.filter(inspection => inspection.completion === (store.getters.completion) && inspection.inspector === store.getters.userID);

                this.inspections = inspections;
                this.address = address[0].street + ', ' + address[0].city;
            } catch (error) {
                console.log(error);
            }
        }
    };
</script>
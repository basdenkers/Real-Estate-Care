<template>
    <main class="container">
        <router-link :to="{ name: 'home' }" class="btn btn-primary mb-4" aria-label="Go back to the dashboard">Go back</router-link>
        <h1>Address List</h1>
        <div class="list-group">
            <a href="#" v-for="address in addresses" :key="address.id" class="list-group-item list-group-item-action d-flex flex-column justify-content-between align-items-center" aria-label="List of addresses">
                {{ address.street }}, {{ address.city }}
                <button class="btn btn-primary" @click="viewInspections(address.id, false)" aria-label="View inspections for this address">View Inspections</button>
            </a>
        </div>
    </main>
</template>
  
<script>
    import axios from 'axios';
    import store from '../store/store.js'

    store.commit('toggleCompletion', false);
    export default {
        name: "AddressList",
        data() {
            return {
                addresses: []
        };
    },
    async created() {

        try {
            const response = await axios.get(`https://api.jsonbin.io/v3/b/63c1a09815ab31599e35cf00/latest`, {
                headers: {
                'Content-Type': 'application/json',
                'X-Master-Key': '$2b$10$6OQ5plkCt1vMLN8m7VMniOP5RSMQB3WOfPoQlYh/JNbs2xeF7psUu'
                }
            });

            this.addresses = response.data.record.addresses;
            
            for (let i = 0; i < response.data.record.addresses.length; i++) {
                let address = response.data.record.addresses[i];
                if (address.inspections === null || address.inspections === undefined) continue;

                let found = false;
                for (let j = 0; j < address.inspections.length; j++) {
                    let inspection = address.inspections[j];
                    if (inspection.inspector === store.getters.userID && inspection.completion === store.getters.completion) {
                        found = true;
                        break;
                    }
                }

                if (!found) {
                    response.data.record.addresses.splice(i, 1);
                    i--;
                }
            }
              

        } catch (error) {
            console.log(error);
        }
    },
    methods: {
        async viewInspections(id, completion) {
            try {
                store.commit("changeAddress", id)
                store.commit("toggleCompletion", completion)
                this.$router.push({ name: 'inspections'});
            } catch (error) {
                console.log(error);
            }
        }
    }
};
</script>
<template>
    <main class="container">
        <div class="row mt-5">
            <div class="col-sm"></div>
            <div class="col-sm">
                <form @submit.prevent="loginRoute" id="login" name="login" method="post">
                    <div class="mb-3">
                        <label for="email" class="form-label" aria-label="Email">Email</label>
                        <input type="email" class="form-control" id="email" name="email" value="admin2@real-estate-care.nl" placeholder="admin2@real-estate-care.nl" aria-describedby="email-help">
                    </div>
                    <div class="mb-3">
                        <label for="password" class="form-label" aria-label="Password">Password</label>
                        <input type="password" class="form-control" id="password" name="password" placeholder="&bull;&bull;&bull;&bull;&bull;&bull;" value="admin123">
                    </div>
                    <div>
                        <input type="submit" value="Login" class="btn btn-primary" aria-label="Login button">
                    </div>
                </form>
            </div>
            <div class="col-sm"></div>
        </div>

        <div v-if="showToast" class="toast-container">
            <div v-bind:class="{'show': showToast}" aria-live="assertive" :class="'toast toast-' + toastType">
                {{ toastMessage }}
            </div>
        </div>

    </main>
</template>

<script>
import axios from 'axios';
import store from '../store/store.js'

export default {
    data() {
        return {
            email: "",
            password: "",
            alert: "",
            showToast: false,
            toastMessage: '',
            toastType: '',
        };
    },
    methods: {
        async loginRoute(submitEvent) {
            try {
                const response = await axios.get('https://api.jsonbin.io/v3/b/63c0345215ab31599e349bb2/latest', {
                    headers: {
                        'X-Master-Key': '$2b$10$6OQ5plkCt1vMLN8m7VMniOP5RSMQB3WOfPoQlYh/JNbs2xeF7psUu'
                    }
                });

                if (response.status !== 200) {
                    this.toastType = 'error';
                    this.toastMessage = 'Could not verify the user';
                    this.showToast = true;

                    setTimeout(() => {
                        this.toastType = '';
                        this.toastMessage = '';
                        this.showToast = false;
                    }, 3000);

                    return;
                }

                const user = response.data.record.filter(user => user.email === submitEvent.target.elements.email.value && user.password === submitEvent.target.elements.password.value);

                if (user.length > 0) {
                    delete user[0].password;
                    store.commit('setUser', user[0]);
                    this.$router.go('/');
                } else {
                    this.toastType = 'error';
                    this.toastMessage = 'There is no user with those credentials found';
                    this.showToast = true;

                    setTimeout(() => {
                        this.toastType = '';
                        this.toastMessage = '';
                        this.showToast = false;
                    }, 3000);
                }
            } catch (err) {
                console.log(err)
                this.toastType = 'error';
                this.toastMessage = (err.code != 'ERR_NETWORK') ? 'There has been an error occurred, contact the developer!' : 'You dont have wifi!';
                this.showToast = true;

                setTimeout(() => {
                    this.toastType = '';
                    this.toastMessage = '';
                    this.showToast = false;
                }, 3000);

                return;
            }
        }
    }
}
</script>

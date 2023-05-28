<template>
    <div>
        <div class="card">
            <div class="card-header">
                <button type="button" @click="destroy" class="btn btn-danger float-right" aria-label="Delete">Delete</button>
            </div>
            <div class="card-body">

                <div class="form-group mb-3">
                    <label for="location">Location</label>
                    <input type="text" v-model="damage.location" class="form-control" id="location" aria-label="location" aria-describedby="location-help">
                </div>

                <div class="form-group mb-3">
                    <label for="new" class="me-2">New</label>
                    <input type="checkbox" v-model="damage.new" class="form-check-input" id="new" aria-label="new">
                </div>

                <div class="form-group mb-3">
                    <label for="type">Type</label>
                    <input type="text" v-model="damage.type" class="form-control" id="type" aria-label="type" aria-describedby="type-help">
                </div>

                <div class="form-group mb-3">
                    <label for="date">Date</label>
                    <input type="date" v-model="damage.date" class="form-control" id="date" aria-label="date" aria-describedby="date-help">
                </div>

                <div class="form-group mb-3">
                    <label for="acuteAction" class="me-2">Acute Action</label>
                    <input type="checkbox" v-model="damage.acuteAction" class="form-check-input" id="acuteAction" aria-label="acute action">
                </div>

                <div class="form-group mb-3">
                    <label for="description">Description</label>
                    <textarea v-model="damage.description" class="form-control" id="description" aria-label="description" aria-describedby="description-help"></textarea>
                </div>

                <div class="form-group mb-3">
                    <label for="photos">Photos</label>
                    <input type="file" ref="photos" @change="uploadPhotos" class="form-control" id="photos" accept="image/*" multiple aria-label="upload photos">
                </div>

                <div class="card mt-3">
                    <div class="card-body">
                        <div class="row">
                            <div v-for="(photo, index) in photos" :key="index" class="col-md-4">
                                <div class="img-container position-relative">
                                    <img :src="photo" class="img-fluid w-50 mb-3 d-block m-auto img-preview" aria-label="photo">
                                    <button type="button" @click="removePhoto(index)" class="btn btn-danger btn-sm position-absolute top-0 end-0 delete-button" aria-label="remove">
                                        remove
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'DamageComponent',
        props: {
            damage: {
            type: Object,
            required: true
            }
        },
        computed: {
            getPhotos() {
                return this.damage.photos;
            }
        },
        data() {
            return {
                photos: this.damage.photos,
            }
        },
        methods: {
            uploadPhotos(event) {
                this.files = Array.from(event.target.files);
                this.damage.photos = [];
                for (let i = 0; i < this.files.length; i++) {
                    let file = this.files[i];
                    let reader = new FileReader();
                    reader.onloadend = e => {
                        this.photos.push(e.target.result);
                        this.damage.photos.push(e.target.result);
                    };
                    reader.readAsDataURL(file);
                }
            },
            removePhoto(index) {
                this.photos.splice(index, 1);
                this.files.splice(index, 1);
                this.updateFileInput();
            },
            updateFileInput() {
                const dataTransfer = new DataTransfer();
                for(let i = 0; i < this.files.length; i++) {
                    dataTransfer.items.add(this.files[i])
                }

                this.$refs.photos.files = dataTransfer.files;
                this.damage.photos = dataTransfer.files;

                if (this.$refs.photos.webkitEntries.length) {
                    this.$refs.photos.dataset.file = `${dataTransfer.files[0].name}`;
                }
            },
            destroy() {
                this.$emit("destroy");
            }
        }
    };
</script>
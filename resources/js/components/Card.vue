<template>
    <card class="flex flex-col">
        <div class="px-3 py-3">
            <h1 class="text-xl font-light">Import {{this.card.resourceLabel}}</h1>
            <form @submit.prevent="processImport">
                <div class="py-4">
                    <span class="form-file mr-4">
                        <input
                                ref="fileField"
                                class="form-file-input"
                                type="file"
                                id="import-file"
                                name="name"
                                @change="fileChange"
                        />
                        <label for="import-file" class="form-file-btn btn btn-default btn-primary">
                            {{__('Choose File')}}
                        </label>
                    </span>
                    <span class="text-gray-50">
                        {{ currentLabel }}
                    </span>

                </div>

                <div class="flex">
                    <div v-if="errors">
                        <p class="text-danger">{{firstError}}</p>
                    </div>
                    <button
                            :disabled="working"
                            type="submit"
                            class="btn btn-default btn-primary ml-auto"
                    >
                        <loader v-if="working" width="30"></loader>
                        <span v-else>{{__('Import')}}</span>
                    </button>
                </div>
            </form>
        </div>
    </card>
</template>

<script>

    export default {
        props: ['card'],

        data() {
            return {
                fileName: '',
                file: null,
                label: 'no file selected',
                working: false,
                errors: null

            }
        },

        mounted() {
            //
        },

        methods: {
            fileChange(event) {
                let path = event.target.value
                let fileName = path.match(/[^\\/]*$/)[0]
                this.fileName = fileName
                this.file = this.$refs.fileField.files[0]
            },
            processImport() {
                this.working = true;
                let formData = new FormData();
                formData.append('file', this.file);
                Nova.request().post('/nova-vendor/sparclex/nova-import-card/endpoint/' + this.card.resource, formData)
                    .then(({data}) => {
                        this.$toasted.success(data.message);
                    })
                    .catch(({response}) => {
                        this.errors = response.data.errors;
                    })
                    .finally(() => this.working = false);
            }
        },
        computed: {
            /**
             * The current label of the file field
             */
            currentLabel() {
                return this.fileName || this.label
            },

            firstError() {
                return this.errors ? this.errors[Object.keys(this.errors)[0]][0] : null;
            }
        }
    }
</script>

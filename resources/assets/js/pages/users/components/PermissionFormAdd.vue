<template>
    <div class="component-wrap">

        <!-- form -->
        <v-card dark>
            <v-form v-model="valid" ref="permissionFormAdd" lazy-validation>
                <v-container grid-list-md>
                    <v-layout row wrap>
                        <v-flex xs12>
                            <div class="body-2 white--text">Permission Details</div>
                        </v-flex>
                        <v-flex xs12>
                            <v-text-field box dark label="Permission Title" v-model="title" :rules="titleRules"></v-text-field>
                        </v-flex>
                        <v-flex xs12>
                            <v-text-field box dark label="Permission Key" v-model="permissionKey" :rules="permissionKeyRules"></v-text-field>
                        </v-flex>
                        <v-flex xs12>
                            <v-text-field box dark label="Description" v-model="description" :rules="descriptionRules" multi-line></v-text-field>
                        </v-flex>
                        <v-flex xs12>
                            <v-btn @click="save()" :loading="isLoading" :disabled="!valid || isLoading" color="primary" dark>Save</v-btn>
                        </v-flex>
                    </v-layout>
                </v-container>
            </v-form>
        </v-card>
        <!-- /form -->

    </div>
</template>

<script>
    export default {
        data() {
            return {
                valid: false,
                isLoading: false,
                title: '',
                titleRules: [
                    (v) => !!v || 'Title is required',
                ],
                description: '',
                descriptionRules: [
                    (v) => !!v || 'Description is required',
                ],
                permissionKey: '',
                permissionKeyRules: [
                    (v) => !!v || 'Permission Key is required',
                    (v) => (v && !v.match(/[^\w\.]+/g)) || 'Description cannot contain special characters',
                ],
            }
        },
        mounted() {
            console.log('components.PermissionFormAdd.vue');
        },
        watch: {
            permissionKey(v) {
                if(v) this.permissionKey = v.replace(' ','.').toLowerCase();
            },
            title(v) {
                if(v) this.permissionKey = v.replace(' ','.').toLowerCase();
            }
        },
        methods: {
            save() {

                const self = this;

                let payload = {
                    title: self.title,
                    description: self.description,
                    permission: self.permissionKey
                };

                self.isLoading = true;

                axios.post('/admin/permissions',payload).then(function(response) {

                    self.$store.commit('showSnackbar',{
                        message: response.data.message,
                        color: 'success',
                        duration: 3000
                    });
                    self.$eventBus.$emit('PERMISSION_ADDED');

                    // reset
                    self.$refs.permissionFormAdd.reset();
                    self.permissions = [];
                    self.isLoading = false;

                }).catch(function (error) {

                    self.isLoading = false;

                    if (error.response) {
                        self.$store.commit('showSnackbar',{
                            message: error.response.data.message,
                            color: 'error',
                            duration: 3000
                        });
                    } else if (error.request) {
                        console.log(error.request);
                    } else {
                        console.log('Error', error.message);
                    }
                });
            }
        }
    }
</script>
<template>
    <v-layout row wrap>
        <v-flex xs5 offset-sm3>
            <v-card>
                <v-flex xs12 sm6 offset-sm3 class="group-custom-login">
                    <img src="~@/assets/spacex-logo-x.png" width="100%">
                    <v-form v-model="valid" ref="form">
                        <v-text-field
                                name="username"
                                v-model="username"
                                :rules="usernameRules"
                                label="Username"
                                id="username"
                                required
                        ></v-text-field>
                        <v-text-field
                                type="password"
                                name="password"
                                v-model="password"
                                :rules="passwordRules"
                                label="Password"
                                id="password"
                                required
                                class="blue--text text--darken-4"
                        ></v-text-field>
                    </v-form>
                    <v-layout row wrap>
                        <v-flex xs9>
                            <v-alert error dismissible transition="scale-transition" v-model="alert">
                                {{ errorMessage }}
                            </v-alert>
                        </v-flex>
                        <v-flex xs3>
                            <p class="text-xs-right">
                                <v-btn type="submit" @click="submit" right
                                       class="blue--text text--darken-4">
                                    <v-progress-circular
                                            v-bind:class="{'is-loading' : !loading }"
                                            v-bind:size="20"
                                            indeterminate
                                            class="blue--text text--darken-4">
                                    </v-progress-circular>
                                    <span v-bind:class="{'is-loading' : loading }">Login</span>
                                </v-btn>
                            </p>
                        </v-flex>
                    </v-layout>
                </v-flex>
            </v-card>
        </v-flex>
    </v-layout>
</template>
<style>
    img {
        display: block;
        margin: 20px auto 20px auto;
        padding-top: 20px;
    }

    .btn--right {
        right: 0 !important;
        margin-right: 0 !important;
    }

    .alert {
        padding: 5px 16px 5px 16px !important;
    }

    .alert > div {
        font-size: 12px;
    }

    .is-loading {
        display: none !important;
    }
</style>
<script>
    import axios from 'axios';
    import config from '../../main/config';

    export default {
        data() {
            return {
                valid: false,
                username: '',
                usernameRules: [
                    v => !!v || 'Name is required'
                ],
                password: '',
                passwordRules: [
                    v => !!v || 'Password is required'
                ],
                alert: false,
                errorMessage: '',
                loading: false
            };
        },
        methods: {
            submit() {
                this.$refs.form.validate();
                if (this.valid === true) {
                    this.loading = true;
                    const userData = {
                        username: this.username,
                        password: this.password
                    };
                    axios({
                        method: 'post',
                        url: `${config.apiUrl}/user/login`,
                        data: userData
                    }).then((response) => {
                        this.loading = false;
                        if (response.data.status === 'success') {
                            this.$router.push('/home');
                        }
                    }).catch((e) => {
                        this.loading = false;
                        this.alert = true;
                        if (typeof (e.response) !== 'undefined') {
                            if (e.response.statusText === 'Unauthorized') {
                                this.errorMessage = 'Wrong username or password.';
                            }
                        } else {
                            this.errorMessage = 'Network Error';
                        }
                    });
                }
            }
        }
    };
</script>
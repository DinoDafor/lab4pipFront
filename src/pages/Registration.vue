<template>
    <div class="registration">
        <form class="registrationForm" @submit.prevent="doRegister">
            <TextInput v-model="form.login">Login:</TextInput>
            <TextInput v-model="form.password">Password:</TextInput>
            <Button type="submit">Познакомиться со Свиньёй</Button>
        </form>
    </div>
</template>


<script>
    import TextInput from "@/components/TextInput";
    import Button from "@/components/Button";
    import axios from "axios";

    export default {
        name: "Registration",
        components: {Button, TextInput},
        data() {
            return {
                form: {
                    login: '',
                    password: ''
                },
            }
        },
        methods: {
            createErrorToast: function (errorMessage, time) {
                this.$toasted.error(errorMessage).goAway(time);
            },
            createSuccessToast: function (successMessage, time) {
                this.$toasted.success(successMessage).goAway(time);
            },
            doRegister: function () {
                if (this.form.login === '') {
                    this.createErrorToast("The login cannot be empty!", 3000)
                } else if (this.form.password === '') {
                    this.createErrorToast("The password cannot be empty!", 3000)
                } else {
                    axios.post('http://192.168.1.42:8080/api/users/register', {
                        login: this.form.login,
                        password: this.form.password,
                        headers: {
                            'Content-Type': 'application/json'
                        },
                    })
                        .then(() => {
                            this.createSuccessToast("You have successfully registered! Please enter your username and password in the form!", 3000);
                            this.$router.push({path: '/login'});
                        })
                        .catch((error) => {

                            if (error.response) {//при ошибке от сервера

                                let statusFromServer = error.response.status;
                                if (statusFromServer === 409) {
                                    //Срабатывает
                                    this.createErrorToast('You are already registered!', 3000);
                                } else if (statusFromServer === 400) {
                                    //Никогда не сработает, потому что не даёт отправить форму, если она пуста
                                    this.createErrorToast('Empty username or password!', 3000);
                                }

                            } else if (error.request) {//при ошибке запроса
                                this.createErrorToast('You are offline, check your Internet connection!', 3000);
                            } else {
                                this.createErrorToast('Unknown error!', 3000);
                            }

                        }).finally(() => {
                       //
                    });
                }


            }
        }
    }
</script>

<style scoped>

</style>
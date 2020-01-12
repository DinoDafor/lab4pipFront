<template>
    <div class="registration">
        <form class="registrationForm" @submit.prevent="doLogin">
            <TextInput v-model="form.login">Login:</TextInput>
            {{form.login}}
            <TextInput v-model="form.password">Password:</TextInput>
            {{form.password}}
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
                errors: [],

            }
        },
        methods: {
            createErrorToast: function (errorMessage, time) {
                this.$toasted.error(errorMessage).goAway(time);
            },
            createSuccessToast: function (successMessage, time) {
                this.$toasted.success(successMessage).goAway(time);
            },
            doLogin: function () {
                if (this.form.login === '') {
                    this.createErrorToast("The login cannot be empty!", 3000)
                } else if (this.form.password === '') {
                    this.createErrorToast("The password cannot be empty!", 3000)
                } else { //сюда надо будет вставить пост запрос
                }
                //todo пока что без валидации происходит отправка, для отладки
                axios.post('http://192.168.1.42:8080/api/users/register', {
                    //заполняем данные для отправки
                    login: this.form.login,
                    password: this.form.password,
                    headers: {
                        'Content-Type': 'application/json'
                    },

                })
                    .then(function (response) {
                        //при ответе от сервера
//todo здесь сделать логику присваивания от сервера + пуш роутера на
                        // this.$router.push({ path: '/main' });
                        //this.createSuccessToast("You have successfully logged in!", 3000)
                        alert(response.data);
                        alert(response.status);
                        alert(response.statusText);
                        alert(response.headers.toString());
                        alert(response.config.toString());
                        //this.response = response;
                    })
                    .catch(function (error) {

                        if (error.response) {//при ошибке от сервера

                            // let status = error.response.status;
                            // let data =error.response.data;


                        } else if (error.request) {//при ошибке запроса

                            alert('!');
                            alert(error.request);
                        } else {
                            //НЕИЗВЕСТНАЯ ОШИБКА
                        }

                        alert(error);
                        this.error = error;
                    }).finally(() => {
                    //Совершаем в любом случае
                    // this.loading = false;
                    // this.form.password = '';
                });


            }
        }
    }
</script>

<style scoped>

</style>
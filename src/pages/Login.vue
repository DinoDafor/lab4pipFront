<template>
    <div class="login">
        <form class="loginForm" @submit.prevent="doLogin">
            {{info}}
            <TextInput v-model="form.login">Login:</TextInput>
            {{form.login}}
            <TextInput v-model="form.password">Password:</TextInput>
            {{form.password}}

            {{response}}
            <br/>
            {{error}}


<!--            <router-link to="/registration">Перейти к Foo</router-link>-->


            <Button type="submit">Поздороваться со Свиньёй</Button>
        </form>
        <Button @click="$toasted.global.loginSuccess"></Button>
    </div>

</template>

<script>
    import Button from "@/components/Button";
    import TextInput from "@/components/TextInput";
    import axios from 'axios'

    import Vue from 'vue';

    import VueRouter from "vue-router";
    import Toasted from 'vue-toasted'

    Vue.use(VueRouter);
    Vue.use(Toasted);
    //Создаём тосты:
    //todo я не разобрался как нормально их использовать, оставлю пока что функции-костыли
    //todo продублировать на REG КОМПОНЕНТ
    Vue.toasted.register('loginEmpty', 'The login cannot be empty!', {
        type: 'error',
        theme: 'toasted-primary',
        duration: 3000,
        position: 'top-right',
        iconPack: 'material',
        icon: 'check', //Тут надо подумать насчёт иконок для тостов
        action: {
            text: 'Hide',
            onClick: (e, toastObject) => { //Функция для скрытия тоста
                toastObject.goAway(0);
            }
        }
    });
    Vue.toasted.register('passwordEmpty', 'The password cannot be empty!', {
        type: 'error',
        theme: 'toasted-primary',
        duration: 3000,
        position: 'top-right',
        iconPack: 'material',
        icon: 'check',
        action: {
            text: 'Hide',
            onClick: (e, toastObject) => {
                toastObject.goAway(0);
            }
        }
    });
    Vue.toasted.register('authenticationError', 'Wrong username or password!', {
        type: 'error',
        theme: 'toasted-primary',
        duration: 3000,
        position: 'top-right',
        iconPack: 'material',
        icon: 'check',
        action: {
            text: 'Hide',
            onClick: (e, toastObject) => {
                toastObject.goAway(0);
            }
        }
    });
    Vue.toasted.register('loginSuccess', 'You have successfully logged in!', {
        type: 'success',
        theme: 'toasted-primary',
        duration: 3000,
        position: 'top-right',
        iconPack: 'material',
        icon: 'check',
        action: {
            text: 'Hide',
            onClick: (e, toastObject) => {
                toastObject.goAway(0);
            }
        }
    });
    //TODO ПЕРЕНЕСТИ НА REG КОМПОНЕНТ
    Vue.toasted.register('registrationSuccess', 'You have successfully registered!', {
        type: 'success',
        theme: 'toasted-primary',
        duration: 3000,
        position: 'top-right',
        iconPack: 'material',
        icon: 'check',
        action: {
            text: 'Hide',
            onClick: (e, toastObject) => {
                toastObject.goAway(0);
            }
        }
    });
    Vue.toasted.register('registrationErrorWithLogin', 'This login already exists!', {
        type: 'error',
        theme: 'toasted-primary',
        duration: 3000,
        position: 'top-right',
        iconPack: 'material',
        icon: 'check',
        action: {
            text: 'Hide',
            onClick: (e, toastObject) => {
                toastObject.goAway(0);
            }
        }
    });
    //todo LOG OUT
    Vue.toasted.register('logOutSuccess', 'You have successfully log out!', {
        type: 'success',
        theme: 'toasted-primary',
        duration: 3000,
        position: 'top-right',
        iconPack: 'material',
        icon: 'check',
        action: {
            text: 'Hide',
            onClick: (e, toastObject) => {
                toastObject.goAway(0);
            }
        }
    });
    //todo сделать тосты на коды ошибок от сервера


    export default {
        name: "Login",
        data() {
            return {
                info: '',
                form: {
                    login: '',
                    password: ''
                },
                response: '))',
                error: '((',
            }
        },
        mounted() {


            axios
                .get('https://api.coindesk.com/v1/bpi/currentprice.json')
                .then(response => (this.info = response));
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
                axios.post('users/login', {
                    //заполняем данные для отправки
                    login: this.form.login,
                    password: this.form.password,

                })
                    .then(function (response) {
                        //при ответе от сервера
//todo здесь сделать логику присваивания от сервера + пуш роутера на мейн
                        // this.$router.push({ path: '/main' });
                        //this.createSuccessToast("You have successfully logged in!", 3000)
                        this.response = response;
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


                        this.error = error;
                    }).finally(() => {
                    //Совершаем в любом случае
                    // this.loading = false;
                    // this.form.password = '';
                });


            }
        },

        components: {Button, TextInput}
    }
</script>

<style scoped>
    .login {

    }

</style>
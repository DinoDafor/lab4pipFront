<template>
    <div id="content">
        <div id="login">
            <form id="loginForm" @submit.prevent="doLogin">
                <img src="http://sun9-49.userapi.com/c851220/v851220524/1d1880/UMLBra64VB4.jpg" width="300" height="300">
                <TextInput v-model="form.login" placeholder="Напомнить свинье своё имя"></TextInput>
                <TextInput v-model="form.password" placeholder="Сказать свинье секретный пароль"></TextInput>
                <Button type="submit">&#9658;</Button>
            </form>
        </div>
    </div>

</template>

<script>
    import Button from "@/components/Button";
    import TextInput from "@/components/TextInput";
    import axios from 'axios'

    import Vue from 'vue';

    import VueRouter from "vue-router";
    import Toasted from 'vue-toasted'
    import {eventBus} from "../main";

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
        // beforeRouteLeave(to, from, next) { //не даём перейти с main
        //     if (this.user.login) {
        //         next()
        //     } else {
        //         next(false)
        //     }
        // },
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
                } else {
                    axios.post('http://192.168.1.42:8080/api/users/login', {
                        //заполняем данные для отправки
                        login: this.form.login,
                        password: this.form.password,
                        headers: {
                            'Content-Type': 'application/json'
                        },

                    })
                        .then((response) => {//при ответе от сервера
                                //todo токен надо присвойть в другое место, в хранилище
                                let token = JSON.stringify(response.data.message);
                                let auth = true;
                                //Отправляем через шину данные в хранилище(App) для того, чтобы хранить и там, и в localStorage
                                eventBus.$emit("changeLoginAndPassword", this.form.login, this.form.password, token, auth);
                                //Уведомляем пользователя
                                this.createSuccessToast("You have successfully logged in! Enjoy!", 3000);
                                //Переадресовываем на main
                                this.$router.push({path: '/main'});
                        })
                        .catch((error) => {

                            if (error.response) {//при ошибке от сервера

                                let statusFromServer = error.response.status;
                                if (statusFromServer === 401) {
                                    //Срабатывает
                                    this.createErrorToast('Wrong username or password!', 3000);
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
        },

        components: {Button, TextInput}
    }
</script>


<style scoped>

    #login {
        position: absolute;
        width: 320px;
        margin:auto
    }

    #loginForm {

        height: 90px;
        width: 300px;
        display: block;
        background: rgb(52, 56, 61);
        content: '';
        top: 44px;
        margin-left: 20px;
        z-index: 1;

    }

    #loginForm Button{
        top: 325px;
        right: -24px;
    }

    #content{
        display: flex;
        justify-content: center;


    }



    @media screen and (max-width : 803px) {
        #loginForm {
            height: 75px;
            width: 250px;
        }

        #loginForm Button{
            top: 269px;
            right: 20px;
        }

        img{
            width: 250px;
            height: 250px;
        }

    }

    @media screen and (min-width : 1138px) {
        #loginForm img{
            width: 450px;
            height: 450px;
        }

        #loginForm Button{
            top: 476.7px;
            right: -177px;
        }

        #loginForm {
            width: 450px;
        }
    }


</style>
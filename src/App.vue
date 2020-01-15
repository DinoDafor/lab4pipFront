<template>
    <div id="app">

        <!--      todo возможно потом надо будет сделать компонент Navigation для отображения навигации, а не просто в App-->

        <div id="menu" v-show="visible">
            <div class="navigation">
                <router-link to="/login">Поздороваться с Кабаном</router-link>
            </div>
            <div class="navigation">
                <router-link to="/register">Познакомиться с Кабаном</router-link>
            </div>
        </div>

        <router-view></router-view>



    </div>
</template>

<script>
    import Login from "./pages/Login";
    import Registration from "./pages/Registration";
    import Vue from 'vue'
    import VueRouter from "vue-router";
    import Main from "./pages/Main";
    import NotFound from "./pages/NotFound";
    import Graph from "./components/Graph";
    import axios from "axios";
    import Vuex from 'vuex';
    import {eventBus} from "./main";


    Vue.use(VueRouter);
    Vue.use(Vuex);
    //todo может ещё какие-то пути-компоненты...
    const routes = [
        {path: '/', redirect: '/login'},
        {path: '/login', component: Login},
        {path: '/register', component: Registration},
        {path: '/main', component: Main},
        {path: '/graph', component: Graph},
        {path: '*', component: NotFound},

    ];


    let router = new VueRouter({
        mode: 'history',
        routes
    });



    export default {
        name: 'app',
        router,
        data() {
            return {
                visible:true,
                user: {
                    auth: false,
                    login: null,
                    password: null,
                    token: null,
                    //    todo Добавить mail?
                },
            }
        },
        created() {
            eventBus.$on("changeLoginAndPassword", (login, password, token, auth) => {
                //Присваиваем данные с компомнента для App, чтобы хранить todo дописать
                this.user.login = login;
                this.user.password = password;
                this.user.token = token;
                this.user.auth = auth;
                //Сохраняем данные в localStorage
                localStorage.setItem('user.login', this.user.login);
                localStorage.setItem('user.password', this.user.password);
                localStorage.setItem('user.token', this.user.token.split("\"").join('').trim());
                localStorage.setItem('user.auth', this.user.auth);


            });
            eventBus.$on("visibleFalse", () => {
                this.visible = false;
            });
            eventBus.$on('visibleTrue', () => {
                this.visible = true;
            })

        },
        methods: {
            load() {
                //Достаём логин и пароль с локального хранилища
                let login = localStorage.getItem('user.login');
                let password = localStorage.getItem('user.password');
                //  let token = localStorage.getItem('user.token');
                //Если они существуют, то мы делаем запрос на сервер для подтверждения данных, после чего пользователь переходит сразу в main
                if (login && password) {
                    axios.post('http://192.168.1.42:8080/api/users/login', {
                        login: login,
                        password: password,
                        headers: {
                            'Content-Type': 'application/json'
                        },
                    }).then((response) => {
                        //todo добавить тост, который поздравляет пользователя с возвращением
                        //todo с localStorage подумать
                        this.user.login = login;
                        this.user.password = password;
                        this.user.token = JSON.stringify(response.data.message);
                        //todo пока что вот так, обновляем при каждом заходе токен, убираем "
                        localStorage.setItem('user.token', this.user.token.split("\"").join('').trim());
                        //todo auth добавить
                        // eventBus.$emit("sendToMain", this.user.login, this.user.password, this.user.token, this.user.auth);
                        this.$router.push({path: '/main'});
                        //todo
                        this.user.auth = true;
                    }).catch((error) => {
                        if (error.response) {
                            let statusFromServer = error.response.status;
                            if (statusFromServer === 401) {
                                //todo добавить тост, который говорит, что нельзя менять локалстор или произошла ошибка с ним
                                //При ошибке авторизации удаляем данные из локального хранилища, так как они являются невалидными
                                localStorage.removeItem('user.login');
                                localStorage.removeItem('user.password');
                                localStorage.removeItem('user.token');
                                localStorage.removeItem('user.auth');

                                this.$router.push({path: '/login'});
                            }
                        }
                    }).finally(() => {
                        // onAppLoaded();
                    });
                } else {
                    //todo добавить тост приветствующий пользователя
                    // onAppLoaded();
                }
            }
        },
        mounted() {
            this.load();
        }
    }
</script>

<style>
    @import url(https://fonts.googleapis.com/css?family=Open+Sans:400,700);
    @import url('https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.2.0/css/font-awesome.min.css');

    body {
        font-family: 'Open Sans', 'sans-serif', 'FontAwesome';
        background-color: #111;
        width: 95vw;
        height: 95vh;
    }

    #menu {
        list-style-type: none;
        overflow: hidden;
        border-style: solid;
        border-color: forestgreen;
        display: flex;
    }

    .navigation {
        float: left;
        flex:auto;
    }

    .navigation a {

        display: block;
        color: forestgreen;
        text-align: center;
        padding: 14px 16px;
        text-decoration: none;
    }

    /* Change the link color to #111 (black) on hover */
    .navigation a:hover {
        background-color: purple;
    }

    @media screen and (max-width: 803px) {
        .navigation a {
            display: block;
            color: forestgreen;
            text-align: center;
            padding: 7px 8px;
            text-decoration: none;
        }
    }
</style>

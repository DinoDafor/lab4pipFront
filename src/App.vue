<template>
    <div id="app">
        <!--    <router-view :key="this.$route.fullPath"/>-->
        <!--      todo возможно потом надо будет сделать компонент Navigation для отображения навигации, а не просто в App-->

        <div class="navigation">
            <router-link to="/login">Знакомы уже с Кабаном?</router-link>
            <router-link to="/register">Не знакомы ещё с Кабаном?</router-link>
            <router-link to="/main">Перейти к Main(Отладка)</router-link>
            <router-link to="/graph">Перейти к Graph(Отладка)</router-link>
        </div>
        <router-view></router-view>

        <!--<router-view></router-view>-->

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


    Vue.use(VueRouter);
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
        //components говорит нам о том, что мы можем использовать эти зарегистрированные компоненты здесь
        components: {}, router,
        data() {
            return {
                user: {
                    auth: false,
                    login: null,
                    password: null,
                    token: null,
                    //    todo Добавить mail?
                },
            }
        },
        methods: {  load() {
                //Для отладки
                // localStorage.setItem('user.login', 'bi');
                // localStorage.setItem('user.password', 'ba');
                //Достаём логин и пароль с локального хранилища
                let login = localStorage.getItem('user.login');
                let password = localStorage.getItem('user.password');
                //  let token = localStorage.getItem('user.token');
                //Если они существуют, то мы делаем запрос на сервер для подтверждения данных, после чего пользователь переходит сразу в main
                if (login && password) {
                    alert("zasli?");
                    axios.post('http://192.168.1.42:8080/api/users/login', {
                        login: login,
                        password: password,
                        headers: {
                            'Content-Type': 'application/json'
                        },
                    }).then((response) => {
                        //todo добавить тост, который поздравляет пользователя с возвращением
                        this.user.login = login;
                        this.user.password = password;
                        this.user.token = JSON.stringify(response.data.message);
                        this.$router.push({path: '/main'});
                        //?
                        this.user.auth = true;
                    }).catch((error) => {
                        if (error.response) {
                            let statusFromServer = error.response.status;
                            if (statusFromServer === 401) {
                                //todo добавить тост, который говорит, что нельзя менять локалстор или произошла ошибка с ним
                                //При ошибке авторизации удаляем данные из локального хранилища, так как они являются невалидными
                                localStorage.removeItem('user.login');
                                localStorage.removeItem('user.password');
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
            }},
        watch: {},

        mounted() {
          this.load();
        }
    }
</script>

<style>
    .navigation {

    }

    /*#app {*/
    /*  font-family: 'Avenir', Helvetica, Arial, sans-serif;*/
    /*  -webkit-font-smoothing: antialiased;*/
    /*  -moz-osx-font-smoothing: grayscale;*/
    /*  text-align: center;*/
    /*  color: #2c3e50;*/
    /*  margin-top: 60px;*/
    /*}*/
</style>

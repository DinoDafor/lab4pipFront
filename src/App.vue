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

    Vue.use(VueRouter);
    //todo может ещё какие-то пути-компоненты...
    const routes = [
        {path: '/', redirect: '/login'},
        {path: '/login', component: Login},
        {path: '/register', component: Registration},
        {path: '/main', component: Main},
        {path:'/graph', component:Graph},
        {path: '*', component: NotFound},

    ];


    let router = new VueRouter({
        mode: 'history',
        routes
    });


    export default {
        name: 'app',
        //components говорит нам о том, что мы можем использовать эти зарегистрированные компоненты здесь
        components: {}, router
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

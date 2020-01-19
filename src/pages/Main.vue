<template>
    <div id="main">
        <div id="menu" v-show="true">
            <div class="navigation">
                <router-link  to="/login" >Попрощаться со свиньёй</router-link>
            </div>
        </div>
        <Graph v-bind:r="r" width="250" height="250" :dots="dots" @updateTable="getDots"></Graph>
        <div id="content">
            <div id="inputs">
                <form id="mainForm" @submit.prevent="sendCoordinates">
                    <select v-model="x">
                        <option disabled value="">Выберите X</option>
                        <option>-2</option>
                        <option>-1.5</option>
                        <option>-1</option>
                        <option>-0.5</option>
                        <option>0</option>
                        <option>0.5</option>
                        <option>1</option>
                        <option>1.5</option>
                        <option>2</option>
                    </select>


                    <TextInput v-model="y" placeholder="Введите Y(-3...3)"></TextInput>

                    <select v-model="r">
                        <option disabled value="">Выберите R</option>
                        <option>-2</option>
                        <option>-1.5</option>
                        <option>-1</option>
                        <option>-0.5</option>
                        <option>0</option>
                        <option>0.5</option>
                        <option>1</option>
                        <option>1.5</option>
                        <option>2</option>
                    </select>
                    <Button type="submit">&#9658;</Button>


                </form>
<!--<Button @click="logOut">Log Out</Button>-->
                <table id="table">
                    <tr>
                        <th>X</th>
                        <th>Y</th>
                        <th>R</th>
                        <th>Result</th>
                    </tr>
                    <tr v-for="dot in dots" v-bind:key="dot">
                        <td>{{dot.newX}}</td>
                        <td>{{dot.newY}}</td>
                        <td>{{dot.newR}}</td>
                        <td>{{dot.newInArea}}</td>
                    </tr>
                </table>
            </div>
        </div>
    </div>


</template>

<script>

    import TextInput from "../components/TextInput";
    import Button from "../components/Button";
    import Graph from "../components/Graph";
    import axios from 'axios'
    import {eventBus} from "../main";


    export default {
        components: {
            Button, TextInput, Graph

        },
        beforeRouteLeave(to, from, next) {
            next();//не даём перейти с main
            this.logOut();

        },
        name: "Main",
        data() {
            return {
                user: {
                    auth: false,
                    login: null,
                    password: null,
                    token: null,

                },
                x: '',
                y: '',
                r: 2,
                dots: [{
                    newX: '',
                    newY: '',
                    newR: '',
                    newInArea: ''
                }
                ],


            }
        },
        methods: {
            createErrorToast: function (errorMessage, time) {
                this.$toasted.error(errorMessage).goAway(time);
            },
            createSuccessToast: function (successMessage, time) {
                this.$toasted.success(successMessage).goAway(time);
            },
            logOut(){
                axios.get('http://192.168.1.42:8080/api/users/logout', {
                    headers: {
                        'Authorization': this.user.token,
                    }
                }); //todo добавить тостер
                localStorage.removeItem('user.login');
                localStorage.removeItem('user.password');
                localStorage.removeItem('user.token');
                localStorage.removeItem('user.auth');

                eventBus.$emit("visibleTrue");
                this.$router.push("/login");
            },
            getDots() {
                this.user.login = localStorage.getItem('user.login');
                this.user.password = localStorage.getItem('user.password');
                this.user.token = localStorage.getItem('user.token');
                this.user.auth = localStorage.getItem('user.auth');
                axios.get('http://192.168.1.42:8080/api/dots/getAll', {
                    headers: {
                        'Content-Type': 'application/json',
                        'Authorization': this.user.token,
                    }
                })
                    .then((response) => {//при ответе от сервера
                        this.dots = [];

                        for (let i in response.data) {
                            let newDot = {
                                newX: response.data[i].x,
                                newY: response.data[i].y,
                                newR: response.data[i].r,
                                newInArea: response.data[i].inarea,
                            };

                            this.dots.push(newDot);
                        }
                    })
                    .catch((error) => {

                        if (error.response) {//при ошибке от сервера

                            let statusFromServer = error.response.status;
                            if (statusFromServer === 401) { //todo не те тосты
                                this.createErrorToast('Неправильный логин или пароль!', 3000);
                            } else if (statusFromServer === 400) {
                                this.createErrorToast('Пустой логин или пароль!', 3000);
                            }

                        } else if (error.request) {//при ошибке запроса
                            this.createErrorToast('Вы оффлайн, проверьте соединение с интернетом!', 3000);
                        } else {
                            this.createErrorToast('Неизвестная ошибка!', 3000);
                        }
                        this.createErrorToast('Ошибка!', 3000);
                    });
            },
            load() {
                //todo Скорее всего придётся заменить

                this.user.login = localStorage.getItem('user.login');
                this.user.password = localStorage.getItem('user.password');
                this.user.token = localStorage.getItem('user.token');
                this.user.auth = localStorage.getItem('user.auth');
                //todo сделать auth false
                //todo добавить тосты на все ошибки
                if (this.user.login === null) {
                    this.createErrorToast('Это демо версия, зарегистрируйтесь пожалуйста!', 3000);
                } else if (this.user.password === null) {
                    this.createErrorToast('Невалидный пароль!', 3000);
                    // } else if (this.user.auth === null) {
                    //     alert("Auth null")
                    //     //this.createErrorToast("The password cannot be empty!", 3000)
                    // } else if (this.user.token === null) {
                    // alert("Token null")
                    //this.createErrorToast("The password cannot be empty!", 3000)
                } else {
                    axios.get('http://192.168.1.42:8080/api/dots/getAll', {
                        headers: {
                            'Content-Type': 'application/json',
                            'Authorization': this.user.token,
                        }
                    })
                        .then((response) => {//при ответе от сервера
                            this.dots = [];

                            for (let i in response.data) {
                                let newDot = {
                                    newX: response.data[i].x,
                                    newY: response.data[i].y,
                                    newR: response.data[i].r,
                                    newInArea: response.data[i].inarea,
                                };

                                this.dots.push(newDot);
                            }
                        })
                        .catch((error) => {

                            if (error.response) {//при ошибке от сервера

                                let statusFromServer = error.response.status;
                                if (statusFromServer === 401) {
                                    this.createErrorToast('Неправильный логин или пароль!', 3000);
                                } else if (statusFromServer === 400) {
                                    this.createErrorToast('Пустой логин или пароль!', 3000);
                                }
                            } else if (error.request) {//при ошибке запроса
                                this.createErrorToast('Вы оффлайн, проверьте соединение с интернетом!', 3000);
                            } else {
                                this.createErrorToast('Неизвестная ошибка!', 3000);
                            }
                            this.createErrorToast('Ошибка!', 3000);
                        });
                }
            },
            sendCoordinates() {
                //todo Скорее всего придётся заменить

                this.user.login = localStorage.getItem('user.login');
                this.user.password = localStorage.getItem('user.password');
                this.user.token = localStorage.getItem('user.token');
                this.user.auth = localStorage.getItem('user.auth');

                this.y = this.y.split(",").join('.').trim();
                this.y = Math.round(this.y * 100) / 100.0;

                //todo сделать auth false
                //todo добавить тосты на все ошибки
                //todo сделать ВАЛИДАЦИЮ НА ОТРИЦАТЕЛЬНЫЙ R
                if (this.user.login === null) {
                    this.createErrorToast('Это демо версия, зарегистрируйтесь пожалуйста!', 3000);
                    //this.createErrorToast("The login cannot be empty!", 3000)
                } else if (this.user.password === null) {
                    this.createErrorToast('Невалидный пароль!', 3000);
                    //this.createErrorToast("The password cannot be empty!", 3000)
                    // } else if (this.user.auth === null) {
                    //     alert("Auth null")
                    //     //this.createErrorToast("The password cannot be empty!", 3000)
                    // } else if (this.user.token === null) {
                    alert("Token null")
                    //this.createErrorToast("The password cannot be empty!", 3000)
                } else if (this.x === '') {
                    this.createErrorToast('Выберите X!', 3000);
                } else if (this.y === '') {
                    this.createErrorToast('Введите Y!', 3000);
                } else if (this.r === '') {
                    this.createErrorToast('Выберите R!', 3000);
                } else if (this.y >= 3 || this.y <= -3) {
                    this.createErrorToast('Введите число, которое входит в диапазон!', 3000);
                } else {
                    axios.post('http://192.168.1.42:8080/api/dots/add', {
                        //заполняем данные для отправки
                        x: this.x,
                        y: this.y,
                        r: this.r,
                    }, {
                        headers: {
                            'Content-Type': 'application/json',
                            'Authorization': this.user.token,
                        }
                    })
                        .then(() => {//при ответе от сервера
                            axios.get('http://192.168.1.42:8080/api/dots/getAll', {
                                headers: {
                                    'Content-Type': 'application/json',
                                    'Authorization': this.user.token,
                                }
                            })
                                .then((response) => {//при ответе от сервера
                                    this.dots = [];

                                    for (let i in response.data) {
                                        let newDot = {
                                            newX: response.data[i].x,
                                            newY: response.data[i].y,
                                            newR: response.data[i].r,
                                            newInArea: response.data[i].inarea,
                                        };

                                        this.dots.push(newDot);
                                    }

                                })
                                .catch((error) => { //get запрос

                                    if (error.response) {//при ошибке от сервера

                                        let statusFromServer = error.response.status;
                                        if (statusFromServer === 401) {//todo не те тосты
                                            this.createErrorToast('Неправильный логин или пароль! (401)', 3000);
                                        } else if (statusFromServer === 400) {
                                            this.createErrorToast('Пустой логин или пароль!', 3000);
                                            // this.createErrorToast('Empty username or password!', 3000);
                                        }

                                    } else if (error.request) {//при ошибке запроса
                                        this.createErrorToast('Вы оффлайн, проверьте соединение с интернетом!', 3000);
                                    } else {

                                        this.createErrorToast('Неизвестная ошибка!', 3000);
                                    }
                                    this.createErrorToast('Ошибка!', 3000);
                                });
                        })
                        .catch((error) => { //post запрос

                            if (error.response) {//при ошибке от сервера

                                let statusFromServer = error.response.status;
                                if (statusFromServer === 401) {
                                    this.createErrorToast('Неправильный логин или пароль! (401)', 3000);
                                } else if (statusFromServer === 400) {

                                    this.createErrorToast('Пустой логин или пароль!', 3000);
                                }

                            } else if (error.request) {//при ошибке запроса
                                this.createErrorToast('Вы оффлайн, проверьте соединение с интернетом!', 3000);
                            } else {
                                this.createErrorToast('Неизвестная ошибка!', 3000);
                            }
                            this.createErrorToast('Введите валидные данные! (400)', 3000);
                        });


                }


            }
        }, mounted() {
          //  this.$refs.pathToLogin.to="/main";

            this.load();
        }


    }
</script>

<style scoped>
    @import url(https://fonts.googleapis.com/css?family=Open+Sans:400,700);
    @import url('https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.2.0/css/font-awesome.min.css');

    #inputs {
        position: absolute;
    }

    #mainForm select {

        border-style: solid;
        border-color: forestgreen;
        border-width: 0.5px;
        font-family: 'Open Sans', 'sans-serif', 'FontAwesome';
        background-color: rgb(28, 30, 33);
        box-shadow: inset -100px -100px 0 rgb(28, 30, 33); /*Prevent yellow autofill color*/
        color: forestgreen;
        display: block;
        width: 100%;
        height: 45px;
        outline: 0;
        top: -2px;
        padding: 0 0 0 20px;
        font-weight: 700;
    }

    Button {
        top: 41.5px;
        right: -25px;
    }

    #content {
        display: flex;
        justify-content: center;
    }

    #table {
        border: 1px solid forestgreen;
        border-collapse: collapse;
        color: forestgreen;
    }

    td, th, tr {
        width: 17.2%;
        text-align: center;
        border: 1px solid forestgreen;
    }

    @media screen and (max-width: 803px) {
        Button {
            top: 32.5px;
            right: -20px;
        }

        #mainForm select {
            width: 252px;
            height: 37.5px;
        }

        #table td {
            width: 21.2%;
        }

        #table tr {
            width: 21.2%;
        }

        #table th {
            width: 21.2%;
        }
    }

    @media screen and (min-width: 1138px) {

        #table td {
            width: 14.4%;
        }

        #table tr {
            width: 14.4%;
        }

        #table th {
            width: 14.4%;
        }

        #mainForm select {
            height: 55px;
            font-size: 20px;
        }

        Button {
            top: 47.5px;
            right: -30px;
        }
    }


</style>
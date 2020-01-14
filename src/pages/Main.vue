<template>
    <div id="main">
<!--        v-bind:dots="$props.dots"-->
        <Graph v-bind:r="r" width="250" height="250" :dots="dots" ></Graph>
        <div id="content">
            <div id="inputs">
<!--                 @click="sendCoordinates"-->
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


                    <TextInput v-model="y"></TextInput>

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
                <table id="table" >
                    <tr>
                        <th>X</th>
                        <th>Y</th>
                        <th>R</th>
                        <th>Result</th>
                    </tr>
                    <tr v-for="dot in dots"  v-bind:key="dot"><td>{{dot.newX}}</td><td>{{dot.newY}}</td><td>{{dot.newR}}</td><td>{{dot.newInArea}}</td></tr>
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


    //import {eventBus} from "../main";


    export default {
        components: {
            Button, TextInput, Graph

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
                r: '',
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
            load(){
                //todo Скорее всего придётся заменить

                this.user.login = localStorage.getItem('user.login');
                this.user.password = localStorage.getItem('user.password');
                this.user.token = localStorage.getItem('user.token');
                this.user.auth = localStorage.getItem('user.auth');
                //todo сделать auth false
                //todo добавить тосты на все ошибки
                //todo добавить валидацию на Y INPUT
                //todo сделать ВАЛИДАЦИЮ НА ОТРИЦАТЕЛЬНЫЙ R
                //todo сделать тосты и отладку ошибок
                if (this.user.login === null) {
                    alert("Логин null")
                    //this.createErrorToast("The login cannot be empty!", 3000)
                } else if (this.user.password === null) {
                    alert("Пароль null")
                    //this.createErrorToast("The password cannot be empty!", 3000)
                    // } else if (this.user.auth === null) {
                    //     alert("Auth null")
                    //     //this.createErrorToast("The password cannot be empty!", 3000)
                    // } else if (this.user.token === null) {
                    // alert("Token null")
                    //this.createErrorToast("The password cannot be empty!", 3000)
                } else {
                    // alert("зашли в отправку")
                    // alert(this.user.token);
                    // alert(this.x);
                    // alert(this.y);
                    // alert(this.r);
                    axios.get('http://192.168.1.42:8080/api/dots/getAll', {
                        headers: {
                            'Content-Type': 'application/json',
                            'Authorization': this.user.token,
                        }
                    })
                        .then((response) => {//при ответе от сервера
                            //todo как правильно брать значения с сервера????
                            // this.dots.newX = [];
                            // this.dots.newY = [];
                            // this.dots.newR = [];
                            // this.dots.newInArea = [];
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
                            // for (let i in this.newX){
                            //     this.newX[i];
                            // }


                            // //todo токен надо присвойть в другое место, в хранилище
                            // let token = JSON.stringify(response.data.message);
                            // //
                            // this.$parent.user.login = this.form.login;
                            // this.$parent.user.token = token;
                            // this.$parent.user.auth = true;
                            // //Сохраняем логин и пароль в локальном хранилище для след авторизации
                            // localStorage.setItem('user.login', this.form.login);
                            // localStorage.setItem('user.password', this.form.password);
                            // this.createSuccessToast("You have successfully logged in! Enjoy!", 3000);
                            // this.$router.push({path: '/main'});
                        })
                        .catch((error) => {

                            if (error.response) {//при ошибке от сервера

                                let statusFromServer = error.response.status;
                                if (statusFromServer === 401) {
                                    alert('');
                                    //Срабатывает
                                    // this.createErrorToast('Wrong username or password!', 3000);
                                } else if (statusFromServer === 400) {
                                    //Никогда не сработает, потому что не даёт отправить y неправильный
                                    // this.createErrorToast('Empty username or password!', 3000);
                                }

                            } else if (error.request) {//при ошибке запроса
                                //  this.createErrorToast('You are offline, check your Internet connection!', 3000);
                            } else {
                                alert("какая нахуй ошибка")
                                //  this.createErrorToast('Unknown error!', 3000);
                            }
                            alert(error);
                        }).finally(() => {
                        //

                    });

                }


            },
            //todo сделать отправку get запроса при заходе на Main
            sendCoordinates() {
                //todo Скорее всего придётся заменить

                this.user.login = localStorage.getItem('user.login');
                this.user.password = localStorage.getItem('user.password');
                this.user.token = localStorage.getItem('user.token');
                this.user.auth = localStorage.getItem('user.auth');
                //todo сделать auth false
                //todo добавить тосты на все ошибки
                //todo добавить валидацию на Y INPUT
                //todo сделать ВАЛИДАЦИЮ НА ОТРИЦАТЕЛЬНЫЙ R
                //todo сделать тосты и отладку ошибок
                if (this.user.login === null) {
                    alert("Логин null")
                    //this.createErrorToast("The login cannot be empty!", 3000)
                } else if (this.user.password === null) {
                    alert("Пароль null")
                    //this.createErrorToast("The password cannot be empty!", 3000)
                    // } else if (this.user.auth === null) {
                    //     alert("Auth null")
                    //     //this.createErrorToast("The password cannot be empty!", 3000)
                    // } else if (this.user.token === null) {
                    alert("Token null")
                    //this.createErrorToast("The password cannot be empty!", 3000)
                } else if (this.x === '') {
                    alert("x не выбран");
                    // this.createErrorToast("The password cannot be empty!", 3000)
                } else if (this.y === '') {
                    alert("y не выбран");
                    //this.createErrorToast("The password cannot be empty!", 3000)
                } else if (this.r === '') {
                    alert("r не выбран");
                    //this.createErrorToast("The password cannot be empty!", 3000)
                } else {
                    // alert("зашли в отправку")
                    // alert(this.user.token);
                    // alert(this.x);
                    // alert(this.y);
                    // alert(this.r);
                    axios.post('http://192.168.1.42:8080/api/dots/add', {
                        //заполняем данные для отправки
                        // login: this.$root.user.login,
                        // password: this.$root.user.password,
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

                            // //todo токен надо присвойть в другое место, в хранилище
                            // let token = JSON.stringify(response.data.message);
                            // //
                            // this.$parent.user.login = this.form.login;
                            // this.$parent.user.token = token;
                            // this.$parent.user.auth = true;
                            // //Сохраняем логин и пароль в локальном хранилище для след авторизации
                            // localStorage.setItem('user.login', this.form.login);
                            // localStorage.setItem('user.password', this.form.password);
                            // this.createSuccessToast("You have successfully logged in! Enjoy!", 3000);
                            // this.$router.push({path: '/main'});
                        })
                        .catch((error) => {

                            if (error.response) {//при ошибке от сервера

                                let statusFromServer = error.response.status;
                                if (statusFromServer === 401) {
                                    alert('kavo');
                                    //Срабатывает
                                    // this.createErrorToast('Wrong username or password!', 3000);
                                } else if (statusFromServer === 400) {
                                    //Никогда не сработает, потому что не даёт отправить y неправильный
                                    // this.createErrorToast('Empty username or password!', 3000);
                                }

                            } else if (error.request) {//при ошибке запроса
                                //  this.createErrorToast('You are offline, check your Internet connection!', 3000);
                            } else {
                                alert("какая нахуй ошибка")
                                //  this.createErrorToast('Unknown error!', 3000);
                            }
                            alert(error);
                        }).finally(() => {
                        //

                    });

                    axios.get('http://192.168.1.42:8080/api/dots/getAll', {
                        headers: {
                            'Content-Type': 'application/json',
                            'Authorization': this.user.token,
                        }
                    })
                        .then((response) => {//при ответе от сервера
                            //todo как правильно брать значения с сервера????
                            // this.dots.newX = [];
                            // this.dots.newY = [];
                            // this.dots.newR = [];
                            // this.dots.newInArea = [];
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
                            // for (let i in this.newX){
                            //     this.newX[i];
                            // }


                            // //todo токен надо присвойть в другое место, в хранилище
                            // let token = JSON.stringify(response.data.message);
                            // //
                            // this.$parent.user.login = this.form.login;
                            // this.$parent.user.token = token;
                            // this.$parent.user.auth = true;
                            // //Сохраняем логин и пароль в локальном хранилище для след авторизации
                            // localStorage.setItem('user.login', this.form.login);
                            // localStorage.setItem('user.password', this.form.password);
                            // this.createSuccessToast("You have successfully logged in! Enjoy!", 3000);
                            // this.$router.push({path: '/main'});
                        })
                        .catch((error) => {

                            if (error.response) {//при ошибке от сервера

                                let statusFromServer = error.response.status;
                                if (statusFromServer === 401) {
                                    alert('');
                                    //Срабатывает
                                    // this.createErrorToast('Wrong username or password!', 3000);
                                } else if (statusFromServer === 400) {
                                    //Никогда не сработает, потому что не даёт отправить y неправильный
                                    // this.createErrorToast('Empty username or password!', 3000);
                                }

                            } else if (error.request) {//при ошибке запроса
                                //  this.createErrorToast('You are offline, check your Internet connection!', 3000);
                            } else {
                                alert("какая нахуй ошибка")
                                //  this.createErrorToast('Unknown error!', 3000);
                            }
                            alert(error);
                        }).finally(() => {
                        //

                    });

                }


            }
        },mounted() {
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
        width: 310px;
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

    @media screen and (max-width: 803px) {
        Button {
            top: 32.5px;
            right: -25px;
        }

        #mainForm select {
            width: 252px;
            height: 37.5px;
        }
    }

#table {border: 1px solid black;
    border-collapse: collapse;}
</style>
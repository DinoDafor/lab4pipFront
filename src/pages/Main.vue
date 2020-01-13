<template>
    <div id="main">
        <Graph></Graph>
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


                    <TextInput placeholder="(-3 ... 3)"></TextInput>

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
            </div>
        </div>
    </div>


</template>

<script>

    import TextInput from "../components/TextInput";
    import Button from "../components/Button";
    import Graph from "../components/Graph";
    import axios from 'axios'


    export default {
        components: {
            Button, TextInput, Graph

        },
        name: "Main",
        data() {
            return {
                x: '',
                y: '',
                r: '',

            }
        },
        methods: {
            sendCoordinates() {
                alert(">?");
                alert();
                if (this.$root.user.login === '') {
                    alert("Нет логина")
                    //this.createErrorToast("The login cannot be empty!", 3000)
                } else if (this.$root.user.password === '') {
                    alert("Нет пароля")
                    //this.createErrorToast("The password cannot be empty!", 3000)
                } else if (this.$root.user.auth === false) {
                    alert("Нет auth")
                    //this.createErrorToast("The password cannot be empty!", 3000)
                } else if (this.$root.user.token === '') {
                    alert("Нет токена")
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
                    alert("зашли в отправку")
                    axios.post('http://192.168.1.42:8080/ api/dots/add', {
                        //заполняем данные для отправки
                        login: this.$root.user.login,
                        password: this.$root.user.password,
                        x: this.x,
                        y: this.y,
                        r: this.r,
                        headers: {
                            'Content-Type': 'application/json'
                        },

                    })
                        .then((response) => {//при ответе от сервера
                            alert(response)
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
                                    //Срабатывает
                                    // this.createErrorToast('Wrong username or password!', 3000);
                                } else if (statusFromServer === 400) {
                                    //Никогда не сработает, потому что не даёт отправить форму, если она пуста
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
        }


    }
</script>

<style scoped>
    @import url(https://fonts.googleapis.com/css?family=Open+Sans:400,700);
    @import url('https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.2.0/css/font-awesome.min.css');

    #inputs {
        position: fixed;
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
        width: 290px;
        height: 45px;
        outline: 0;
        top: -2px;
        padding: 0 0 0 20px;
        font-weight: 700;
    }

    Button {
        top: 41.5px;
        right: -14px;
    }

    #content {
        display: flex;
        justify-content: center;
    }


</style>
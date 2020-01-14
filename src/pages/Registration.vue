<template>
    <div id="content">
    <div id="registration">
        <form id="registrationForm" @submit.prevent="doRegister">
            <img src="http://sun9-49.userapi.com/c851220/v851220524/1d1880/UMLBra64VB4.jpg" width="300" height="300">
            <TextInput v-model="form.login" placeholder="Представиться свинье"></TextInput>
            <TextInput v-model="form.password" placeholder="Придумать свой пароль">

            </TextInput>
            <Button type="submit">&#9658;</Button>
        </form>
    </div>
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
    #registration {
        position: absolute;
        width: 320px;
        margin:auto
    }

    #registrationForm {
        height: 90px;
        width: 300px;

        display: block;
        background: rgb(52, 56, 61);
        content: '';
        top: 44px;
        margin-left: 20px;
        z-index: 1;
    }

    #content{
        display: flex;
        justify-content: center;
    }

    #registrationForm Button{
        top: 325px;
        right: -24px;
    }

    @media screen and (max-width : 803px) {
        #registrationForm Button{
            top: 269px;
            right: 20px;
        }

        #registrationForm {
            height: 75px;
            width: 250px;
        }

        img{
            width: 250px;
            height: 250px;
        }
    }

    @media screen and (min-width : 1138px) {
        #registrationForm img{
            width: 450px;
            height: 450px;
        }

        #registrationForm Button{
            top: 476.7px;
            right: -177px;
        }

        #registrationForm {
            width: 450px;
        }
    }
</style>
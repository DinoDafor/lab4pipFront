<template>
    <div class="registration">
        <form class="registrationForm">
        <TextInput v-model="form.login">Login:</TextInput>
        {{form.login}}
        <TextInput v-model="form.password">Password:</TextInput>
        {{form.password}}
        <Button>Познакомиться со Свиньёй</Button>
        </form>
    </div>
</template>


<script>
    import TextInput from "@/components/TextInput";
    import Button from "@/components/Button";
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
    }
</script>

<style scoped>

</style>
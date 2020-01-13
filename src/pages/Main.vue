<template>
    <div class="main">
        <Graph></Graph>

        <form class="mainForm">
            X:<select v-model="x">
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
            <span>Выбрано {{x}}</span>
            Y:
            <TextInput placeholder="(-3 ... 3)"></TextInput>

            R:<select v-model="r">
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
            <span>Выбрано {{r}}</span>
            <Button>Отправить</Button>


        </form>
        <Button @submit=" this.$root.user.auth"></Button>
    </div>
</template>

<script>

    import TextInput from "../components/TextInput";
    import Button from "../components/Button";
    import Graph from "../components/Graph";
    // import UserWindow from "../components/UserWindow";


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


    }
</script>

<style scoped>

</style>
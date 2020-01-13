<template>
    <div id="main">
        <Graph></Graph>
        <div id="content">
            <div id="inputs">
                <form id="mainForm">
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
                    <Button>&#9658;</Button>


                </form>
            </div>
        </div>
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
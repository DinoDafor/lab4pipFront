<template>
    <div class="input">
        <slot></slot>
        <input :type="type ? type : 'text'"
               v-model="localText"
               @keydown.esc="$event.target.blur()"
        placeholder="placeholder" class="text ">
    </div>


</template>

<script>
    export default {
        name: "TextInput",
        props: ['hint', 'value', 'prefix', 'errorHint', 'type', 'placeholder'],
        data() {
            return {
                localText: ''
            }
        },
        watch: {
            value() {
                this.localText = this.value;
            },
            localText() {
                this.$emit('input', this.localText);
            }
        }
    }
</script>

<style scoped>
    @import url(https://fonts.googleapis.com/css?family=Open+Sans:400,700);
    @import url('https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.2.0/css/font-awesome.min.css');
    .text {
        border-style: solid;
        border-color: forestgreen;
        border-width: 0.5px;

        font-family: 'Open Sans', 'sans-serif', 'FontAwesome';
        background-color: rgb(28, 30, 33);
        box-shadow: inset -100px -100px 0 rgb(28, 30, 33); /*Prevent yellow autofill color*/
        color: forestgreen;
        display: block;
        width: 280px;
        height: 45px;
        outline: 0;
        top: -2px;
        padding: 0 0 0 20px;
        font-weight: 700;
    }
    ::placeholder{
        color: forestgreen;
    }

    @media screen and (max-width : 803px) {
        .text {
            width: 233px;
            height: 37.5px;

        }
    }

</style>
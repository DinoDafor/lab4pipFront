<template>
    <button :type="type" @click="$emit('click' )" class="click">
        <slot/>
    </button>

</template>

<script>
    export default {
        //"button | reset | submit"
        // Тип по дефолту будет баттон, чтобы добиться большей абстрактности, потом просто буду передовать биндить type='Входной параметр(submit,reset)';
        type: {default: 'button'}

    }
</script>

<style>
    .click{
        display: block;
        position: absolute;
        width: 52px;
        height: 52px;
        color: forestgreen;
        border-radius: 50px;
        outline: 0;
        z-index: 2;

        border: 6px solid forestgreen;
        font-size: 25px;
        text-indent: 0px;
        padding-left: 9px;
        padding-bottom: 3px;
        text-align: center;
        background: rgb(28, 30, 33);
        box-shadow: inset -100px -100px 0 rgb(28, 30, 33);
    }

    .click:hover{
        color: purple;
        text-shadow: 0 0 10px purple;
        cursor: pointer;
    }

    @media screen and (min-width : 1138px) {
        .click{
            width: 70px;
            height: 70px;
            font-size: 35px;
            border: 7px solid forestgreen;
        }
    }

</style>
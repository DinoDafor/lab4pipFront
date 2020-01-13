<template>
    <button :type="type" @click="$emit('click' )" class="click">
        <slot/>
        <!--        slot нужен для вставки "названия" кнопки - "Отправить", "Сбросить" -->
        <!--        v-bind является связкой компонента и html -->
        <!--        : является сокращением v-bind-->
        <!--        @ является сокращеним v-on -->
        <!--        v-on означает "при нажатии"-->
        <!--        $emit нужен для передачи события, имя события-click -->

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
</style>
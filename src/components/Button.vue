<template>
    <button :type="type" @click="$emit('click')">
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

<style scoped lang="scss">
    button {
        margin:  0 25% 0 25% ;
        padding: 10px 15px;
        border-radius: 4px;
        border: 1px #bbb solid;
        background: #0be500;
        cursor: pointer;
        outline: none;
        user-select: none;

        &:active {
            background: rgb(180, 180, 180);
        }

        &[ghost] {
            border-color: white;
            border-width: 2px;
            background: rgba(255, 255, 255, .25);
        }

        &[disabled] {
            opacity: 0.5;
        }

        &[green] {
            background-color: #adffc3;
            border: 1px #82bf91 solid;

            &:active {
                background-color: #79b389;
            }
        }
    }
</style>
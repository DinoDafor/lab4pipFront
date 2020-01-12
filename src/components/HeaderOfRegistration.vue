<template>
    <div class="headerOfRegistration">
        <button type="submit">
            <slot>Залогиниться</slot>
        </button>
    </div>
</template>

<script>
    export default {
        name: "HeaderOfRegistration",
        props: {
            headerLogin: String,
            headerRegister: String
        }
    }
</script>

<style scoped>


</style>
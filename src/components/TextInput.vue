<template>
    <div class="input">
        <slot></slot>
        <input :type="type ? type : 'text'"
               v-model="localText"
               @keydown.esc="$event.target.blur()"
        placeholder="placeholder">
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
    .input {

        margin: 5% 25% 5%;
    }

</style>
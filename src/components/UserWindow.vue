<template>
    <div id="dynamic-component-demo" class="demo">
        <button
                v-for="tab in tabs"
                v-bind:key="tab"
                v-bind:class="['tab-button', { active: currentTab === tab }]"
                v-on:click="currentTab = tab"
        >{{ tab }}</button>

<!--        key, указываем, по чему мы различаем объекты, поскольку только стринг у нас, этого хватает-->

        <component
                v-bind:is="currentTabComponent"
                class="tab"
        ></component>
    </div>
</template>

<script>

    import Login from "../pages/Login";
    import Registration from "../pages/Registration";


   



    export default {
        name: "UserWindow",
        components: {Login,Registration},
        data: function () {
            return {
                currentTab:'Login',
                tabs: ['Login', 'Registration'],
            };
        },
        computed: {
            currentTabComponent: function () {
                return  this.currentTab.toLowerCase()
            }
        },
    }
</script>

<style scoped>
    /* Decs > 1138px, планшет больше 803, но меньше 1138 пикселей, мобильный меньше 803   */

    .demo {
        margin:10% 25% 25% 25%;
       width: 50%;
    }
    .tab-button {
        padding: 6px 10px;
        border-top-left-radius: 3px;
        border-top-right-radius: 3px;
        border: 1px solid #ccc;
        cursor: pointer;
        background: #f0f0f0;
        margin-bottom: -1px;
        margin-right: -1px;
    }
    .tab-button:hover {
        background: #0a6100;
    }
    .tab-button.active {
        background: #0be500;
    }
    .tab {
        border: 1px solid #0be500;
        padding: 10px;

    }
</style>
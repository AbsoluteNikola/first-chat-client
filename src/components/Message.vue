<template>
    <div>
        <p class="title is-5">{{ name }}</p>
        <div class="subtitle is-6"> {{ data }}</div>
        <div style="height: 5px"></div>
    </div>
</template>

<script>
    export default {
        name: "Message",
        props: ['data', 'name']
    }
</script>

<style scoped>

</style>
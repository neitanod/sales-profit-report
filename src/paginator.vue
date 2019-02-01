<template>
        <div>[ THIS IS THE PAGINATOR ]</div>
</template>
<script>
export default {
    props: {
        page: {
            type: [Number, String],
            default: 1
        },
        pages: {
            type: [Number, String],
            default: 1
        },
        links: {
            type: [Number, String],
            default: 5
        },
        next: {
            type: [Boolean, String],
            default: false
        },
        prev: {
            type: [Boolean, String],
            default: false
        },
        first: {
            type: [Boolean, String],
            default: false
        },
        last: {
            type: [Boolean, String],
            default: false
        },
    },
    data: function() {
        return {
        }
    },
    methods: {
        buttonsBefore() {
            ;
        },
        buttonsAfter() {
            ;
        }
    },
    mounted() {
    },
}
</script>
<style>
</style>

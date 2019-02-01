<template>
    <ul class="paginator">
        <li class="paginate-button"><a v-if="first" href="#1">|&lt; </a></li>
        <li class="paginate-button"><a v-if="prev" :href="'#'+(page-1)">&lt; </a></li>
        <li class="paginate-button" v-for="(button, i) in linksBefore()"> <a :href="'#'+button" :key="i">{{ button }} </a></li>
        <li class="paginate-button"><span class="paginator-current-page">{{ page }} </span></li>
        <li class="paginate-button" v-for="(button, i) in linksAfter()" > <a :href="'#'+button" :key="i">{{ button }} </a></li>
        <li class="paginate-button"><a v-if="next" :href="'#'+(page*1+1)">&gt; </a></li>
        <li class="paginate-button"><a v-if="last" :href="'#'+pages">&gt;| </a></li>
    </ul>
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
            links_before: 0,
            links_after: 0,
        }
    },
    methods: {
        linksBefore() {
            let o=[];
            this.calculateLinks();
            for(let i=this.page-1; i>=(this.page-this.links_before); i--) {
                o.unshift(i);
            }
            return o;
        },
        linksAfter() {
            let o=[];
            this.calculateLinks();
            for(let i=(this.page*1)+1; i<=(this.page*1)+this.links_after; i++) {
                o.push(i);
            }
            return o;
        },
        calculateLinks() {
            let middle_after  = Math.floor(this.links/2);
            let links_is_even = middle_after == this.links/2;
            let middle_before = links_is_even ? middle_after-1 : middle_after;

            let corner_after  = Math.min(this.pages-this.page, middle_after);
            let corner_before = Math.min(this.page-1, middle_before);

            this.links_after  = corner_after+(middle_before-corner_before);
            this.links_before = corner_before+(middle_after-corner_after);
        }
    },
    mounted() {
    },
}
</script>
<style>
.paginator ul li {
    display: inline;
}
.paginator .paginator-current-page {
    color: #555555;
}
</style>

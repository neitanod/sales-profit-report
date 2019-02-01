<template>
    <ul class="paginator">
        <li class="paginate-button first"><a v-if="first" @click.prevent="change(1)" href="#1">|&lt; </a></li>
        <li class="paginate-button prev" ><a v-if="prev"  @click.prevent="change(page-1)" :href="'#'+(page-1)">&lt; </a></li>
        <li class="paginate-button" v-for="(button, i) in linksBefore()" :key="i"> <a @click.prevent="change(button)" :href="'#'+button">{{ button }} </a></li>
        <li class="paginate-button current"><span class="paginator-current-page">{{ page }} </span></li>
        <li class="paginate-button" v-for="(button, i) in linksAfter()" :key="i"> <a @click.prevent="change(button)" :href="'#'+button">{{ button }} </a></li>
        <li class="paginate-button next" ><a v-if="next"  @click.prevent="change(page*1+1)" :href="'#'+(page*1+1)">&gt; </a></li>
        <li class="paginate-button last" ><a v-if="last"  @click.prevent="change(pages)" :href="'#'+pages">&gt;| </a></li>
    </ul>
</template>
<script>
export default {
    props: {
        value: {
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
            page: 1,
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

            this.links_after  = Math.min(corner_after, corner_after+(middle_before-corner_before));
            this.links_before = Math.min(corner_before, corner_before+(middle_after-corner_after));
        },
        change(page) {
            this.$emit('input', page*1); // Emit as number
        }
    },
    mounted() {
        this.page = this.value; // props are immutable, we need to use data
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

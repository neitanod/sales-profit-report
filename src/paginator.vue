<template>
    <ul class="paginator">
        <li v-if="first"  @click="change(1)" class="paginate-button first"><a @click.prevent="" href="#1">
                <i class="fa fa-angle-double-left"></i>
            </a></li>
        <li v-if="prev" @click="change(page-1)" class="paginate-button prev" ><a @click.prevent="" :href="'#'+(page-1)">
                <i class="fa fa-angle-left"></i>
            </a></li>
        <li class="paginate-button" v-for="button in linksBefore()" @click="change(button)" :key="button"> <a @click.prevent="" :href="'#'+button">{{ button }} </a></li>
        <li class="paginate-button current"><span class="paginator-current-page">{{ page }} </span></li>
        <li class="paginate-button" v-for="button in linksAfter()" @click="change(button)" :key="button"> <a @click.prevent="" :href="'#'+button">{{ button }} </a></li>
        <li v-if="next" @click="change(page*1+1)" class="paginate-button next" ><a @click.prevent="" :href="'#'+(page*1+1)">
                <i class="fa fa-angle-right"></i>
            </a></li>
        <li v-if="last" @click="change(pages)" class="paginate-button last" ><a @click.prevent="" :href="'#'+pages">
                <i class="fa fa-angle-double-right"></i>
            </a></li>
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
                if(i>=1) {
                    o.unshift(i);
                }
            }
            return o;
        },
        linksAfter() {
            let o=[];
            this.calculateLinks();
            for(let i=(this.page*1)+1; i<=(this.page*1)+this.links_after; i++) {
                if(i<=this.pages) {
                    o.push(i);
                }
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
        },
        change(page) {
            // we are now using v-model=
            // (two way bind explained here: http://vmodel.ip1.cc)
            // instead of :page=
            this.enforce_limits();
            this.$emit('input', page*1); // Emit as number
        },
        enforce_limits() {
            if(this.page>this.pages) { this.page = this.pages; }
            if(this.page<1) { this.page = 1; }
        }
    },
    mounted() {
        this.page = this.value;
        this.enforce_limits();
    },
    watch: {
        value: function(new_val, old_val) {
            this.page = new_val;
            this.enforce_limits();
            // props are not really immutable, it's just forbidden for us to mutate them.
            // (It would break the data flow.  Communication UP should be achieved via events only)
            // Parent component can mutate our props, though.
        },
        pages: function(new_val, old_val) {
            this.enforce_limits();
        }
    }
}
</script>
<style>
@import url(//maxcdn.icons8.com/fonts/line-awesome/1.1/css/line-awesome-font-awesome.min.css);
.paginator ul li {
    display: inline;
}
.paginator .paginator-current-page {
    color: #555555;
}
.paginator .paginate-button {
    color: #898b96;
    border-radius: 50%;
    cursor: pointer;
    justify-content: center;
    height: 2.25rem;
    min-width: 2.25rem;
    align-items: center;
    text-align: center;
    vertical-align: middle;
    padding: 0.5rem;
    text-align: center;
    position: relative;
    font-size: 13px;
    line-height: 1rem;
    font-weight: 400;
    margin: 5px;
}
.paginator .paginate-button a,
.paginator .paginate-button .paginator-current-page {
    min-width: 19px;
    display: inline-block;
    padding: 5px;
}
.paginator .paginate-button a {
    color: #898b96;
}
.paginator .paginate-button .paginator-current-page {
    color: #ffffff;
}
.paginator .paginate-button.current {
    background-color: #716aca;
}
.paginator .paginate-button.first,
.paginator .paginate-button.last,
.paginator .paginate-button.prev,
.paginator .paginate-button.next {
    background-color: #ebe9f2;
}
.paginator .paginate-button.first a,
.paginator .paginate-button.last a,
.paginator .paginate-button.prev a,
.paginator .paginate-button.next a {
    color: #575962;
    padding: 0;
}
.paginator .paginate-button:hover {
    background-color: #716aca;
}
.paginator .paginate-button:hover a {
    color: #ffffff;
    text-decoration: none;
}
.paginator .paginate-button.disabled {
    opacity: 0.6;
}
</style>

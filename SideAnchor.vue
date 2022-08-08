<template>
    <section class="side-anchor">
        <ul v-show="list.length > 0">
            <li 
                v-for="(item, index) in list" 
                :key="index" 
                :style="{ 'padding-left': item.prefix }">
                <a 
                    :href="item.href" 
                    :class="['side-anchor-link', { 'active': index === activeIndex }]">
                    {{ item.content }}
                </a>
            </li>
        </ul>
    </section>
</template>

<script>
import { debounce } from 'lodash-es'
import Vue from 'vue'
export default {
    data() {
        return {
            title: '',
            activeIndex: 0,
            list: [],
        }
    },

    methods: {
        getAnchorList() {
            if (this.$page.title === this.title && this.list.length > 0) return;
            this.title = this.$page.title;

            let dom_list = document.querySelectorAll('h2 .header-anchor,h3 .header-anchor');
            if (dom_list.length === 0) {
                this.list = [];
                return;       
            }
            
            let baseLine = Number(dom_list[0].parentNode.tagName.slice(1)) + 1;
            let list = Array().map.call(
                dom_list,
                value => ({ 
                    content: value.parentNode.innerText.slice(1),
                    href: value.href,
                    prefix: (value.parentNode.tagName.slice(1) - baseLine) * 0.5 + 'rem',
                    offsetTop: value.parentNode.offsetTop,
                    active: false
                })
            )

            this.list = list;
        },

        freshAnchor() {
            if (document.title !== this.title) this.getAnchorList();

            let scrollTop = window.pageYOffset;
            let innerhHeight = window.innerHeight;
            let scope = [
                scrollTop,
                scrollTop + innerhHeight / 3
            ];
            let middleLine = scrollTop + innerhHeight / 2;
            let list = this.list;
            let nextActive = -1;
            list.forEach(
                (value, index) => {
                    if (nextActive === -1 && value.offsetTop > scope[0] && value.offsetTop < scope[1]) nextActive = index;
                    if (nextActive === -1  && middleLine < value.offsetTop) nextActive = index - 1;
                }
            )
            if (nextActive === -1 || this.activeIndex === nextActive) return;
            list.forEach(value => value.active = false);
            list[nextActive].active = true;
            this.activeIndex = nextActive;
        }
    },

    mounted() {
        this.title = this.$page.title;
        window.onload = this.getAnchorList;
        window.onscroll = debounce(this.freshAnchor);
    },

    watch: {
        "$page.path.title": {
            handler: function() {
                Vue.nextTick(this.getAnchorList);
            },
            deep: true
        }
    }
}
</script>

<style>
.side-anchor {
    position: fixed;
    right: 1rem;
    top: 120px;
    z-index: 1;
    width: 8rem;
    max-height: calc(100% - 135px);
    overflow: auto;
    border-left: 0.5rem #2c3e50 solid;
    font-size: 14px;
    font-weight: 500;
    transition: max-height 0.25s;
    scrollbar-width: none;
    -ms-overflow-style: none;
}
.side-anchor ul {
    list-style: none;
    margin: 0;
    padding: 0.5rem;
}
ul .side-anchor-link {
    text-decoration: none;
    color: black;
}
ul .side-anchor-link:hover {
    color: #039be5;
}
.side-anchor-link.active {
    color: #0277bd;
}
.side-anchor::-webkit-scrollbar { 
    width: 0 
}
@media (max-width: 1200px) {
  .side-anchor {
    display: none;
  }
}
</style>
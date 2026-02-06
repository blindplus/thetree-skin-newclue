<template>
    <div
        v-if="mainButtons.length"
        class="content-tools"
        :class="{ 'no-admin-convenience': !$store.state.localConfig['clue.admin_convenience'] }"
    >
        <div class="btn-group">
            <template v-for="(btn, i) in mainButtons" :key="'main-' + i">
                <a
                    v-if="btn.onclick"
                    href="#"
                    @click.prevent="btn.onclick"
                    :class="['btn', 'btn-secondary', 'tools-btn', btn.class]"
                    v-html="btn.html"
                    :title="btn.tooltip"
                />
                <nuxt-link
                    v-else
                    :to="btn.to"
                    :class="['btn', 'btn-secondary', 'tools-btn', btn.class]"
                    v-html="btn.html"
                    :title="btn.tooltip"
                />
            </template>

                    <div
                        v-if="menuButtons.length"
                        :class="['dropdown', 'btn', 'btn-secondary', 'tools-btn', 'tools-menu', { show: menuOpen }]"
                        v-on-click-outside="closeMenu"
                    >
                        <div class="menu-drop" @click="toggleMenu">
                            <span class="fa fa-solid fa-ellipsis-vertical menu-drop-btn"></span>
                        </div>
                        <div class="open" v-show="menuOpen" @click.self="closeMenu">
                            <div class="dropdown-menu dropdown-menu-right" role="menu">
                                <template v-for="(btn, i) in menuButtons" :key="'menu-' + i">
                                    <a
                                        v-if="btn.onclick"
                                        href="#"
                                        @click.prevent="handleMenuClick(btn)"
                                        class="dropdown-item"
                                        v-html="btn.html"
                                    />
                                    <nuxt-link
                                        v-else
                                        :to="btn.to"
                                        class="dropdown-item"
                                        v-html="btn.html"
                                        @click.native="closeMenu"
                                    />
                                </template>
                            </div>
                        </div>
                    </div>
        </div>
    </div>
</template>

<script>
import Common from '~/mixins/common';

export default {
    mixins: [Common],
    data() {
        return {
            menuOpen: false
        }
    },
    methods: {
        toggleMenu() {
            this.menuOpen = !this.menuOpen;
        },
        closeMenu() {
            this.menuOpen = false;
        },
        handleMenuClick(btn) {
            this.closeMenu();
            if (typeof btn.onclick === 'function') {
                btn.onclick();
            }
        }
    },
    computed: {
        page() { return this.$store.state.page; },
        data() { return this.page.data; },
        viewName() { return this.page.viewName; },
        mainButtons() {
            const list = [];
            if (!this.data) return list;
            const doc = this.data.document;

            if (this.viewName === 'wiki') {
                if (this.data.date === null) {
                     list.push({
                        to: this.doc_action_link(doc, 'backlink'),
                        html: `<span style="font-size: 1rem;margin-right: 3px;" class="fa fa-solid fa-anchor"></span> 역링크`
                     });
                      list.push({
                        to: this.doc_action_link(doc, 'acl') + '#namespace.read',
                        html: `<span style="font-size: 1rem;margin-right: 3px;" class="fa fa-solid fa-file-shield"></span> ACL`
                      });
                } else {
                    if (this.data.starred) {
                         list.push({
                            to: this.doc_action_link(doc, 'member/unstar'),
                            tooltip: "Unstar",
                            class: 'starred_1',
                            html: `<span class="fa fa-solid fa-star" style="color: #fff"></span> <span class="star-count">${this.data.star_count}</span>`
                         });
                    } else if (this.data.star_count >= 0) {
                         list.push({
                            to: this.doc_action_link(doc, 'member/star'),
                            tooltip: "Star",
                            html: `<span class="fa fa-regular fa-star"></span> <span class="star-count">${this.data.star_count}</span>`
                         });
                    }
                    
                    if (this.data.editable === true && this.data.edit_acl_message) {
                         list.push({
                            onclick: () => {
                                this.$store.state.viewData.errorAlert = this.data.edit_acl_message;
                                window.scrollTo(0, 0);
                            },
                            html: `<span style="font-size: 1rem;margin-right: 3px;" class="fa fa-solid fa-code-pull-request"></span> 편집 요청`
                         });
                    } else if (this.data.editable === false && this.data.edit_acl_message) {
                         list.push({
                             onclick: () => {
                                 this.$store.state.viewData.errorAlert = this.data.edit_acl_message;
                                 window.scrollTo(0, 0);
                             },
                             html: `<span style="font-size: 1rem;margin-right: 3px;" class="fa fa-solid fa-lock"></span> 편집`
                         });
                    } else {
                         list.push({
                            to: this.doc_action_link(doc, 'edit'),
                            html: `<span style="font-size: 1rem;margin-right: 3px;" class="fa fa-solid fa-edit"></span> 편집`
                         });
                    }
                    
                     list.push({
                        to: this.doc_action_link(doc, 'discuss'),
                        class: this.data.discuss_progress ? 'btn-discuss-progress' : '',
                        html: this.data.discuss_progress 
                              ? `<span style="font-size: 1rem;margin-right: 3px;" class="fa fa-solid fa-commenting"></span> 토론 중`
                              : `<span style="font-size: 1rem;margin-right: 3px;" class="fa fa-solid fa-comment"></span> 토론`
                     });
                     
                     list.push({
                        to: this.doc_action_link(doc, 'history'),
                        html: `<span style="font-size: 1rem;margin-right: 3px;" class="fa fa-solid fa-calendar"></span> 역사`
                     });
                }
            } else {
                if(doc) {
                    list.push({
                        to: this.doc_action_link(doc, 'w'),
                        html: `<span style="font-size: 1rem;margin-right: 3px;" class="fa fa-solid fa-file"></span> 문서`
                    });
                }
            }
            return list;
        },
        menuButtons() {
            const list = [];
            if (!this.data) return list;
            const doc = this.data.document;
            
            if (this.viewName === 'wiki' && this.data.date !== null) {
                 list.push({
                    to: this.doc_action_link(doc, 'backlink'),
                    html: `<span style="font-size: 1rem;margin-right: 3px;" class="fa fa-solid fa-anchor"></span> 역링크`
                 });
                 list.push({
                    to: this.doc_action_link(doc, 'acl'),
                    html: `<span style="font-size: 1rem;margin-right: 3px;" class="fa fa-solid fa-file-shield"></span> ACL`
                 });
                 list.push({
                    to: this.doc_action_link(doc, 'move'),
                    html: `<span style="font-size: 1rem;margin-right: 3px;" class="fa fa-solid fa-suitcase-rolling"></span> 문서 이동`
                 });
                 list.push({
                    to: this.doc_action_link(doc, 'delete'),
                    html: `<span style="font-size: 1rem;margin-right: 3px;" class="fa fa-solid fa-trash"></span> 문서 삭제`
                 });
            }
            return list;
        }
    }
}
</script>

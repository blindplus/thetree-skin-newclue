<template>
<div id="search">
  <form id="searchform" class="form-inline" @submit.prevent>
    <div class="input-group">
      <span class="input-group-btn">
        <nuxt-link to="/random">
          <button class="btn btn-secondary random-btn" type="button"><span class="fa-solid fa-random random-btn-txt"></span></button>
        </nuxt-link>
      </span>
      <div class="input-search">
        <input ref="input" type="search" name="q" placeholder="검색을 시작해보세요" accesskey="f" class="form-control" id="searchInput" autocomplete="off"
               v-model="searchText" @keydown.enter="goSearch">
      </div>
      <span class="input-group-btn">
          <button type="submit" class="btn btn-secondary" @click="goSearch"><span class="fa fa-solid fa-search"></span></button>
          <button type="button" class="btn btn-secondary" @click="goView"><span class="fa fa-solid fa-arrow-right"></span></button>
      </span>
    </div>
  </form>
</div>
</template>

<script>
import Common from '~/mixins/common';

export default {
    mixins: [Common],
    data() {
        return {
            searchText: ''
        }
    },
    methods: {
        goSearch() {
            if(!this.searchText) return;
            this.$router.push({ path: '/Search', query: { q: this.searchText } });
        },
        goView() {
            if(!this.searchText) return;
            this.$router.push(this.doc_action_link(this.searchText, 'w'));
        }
    }
}
</script>

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
               v-on:input="searchText = $event.target.value" v-model="searchTextModel" @blur="blur" @focus="focus" @input="inputChange" @keydown.enter="keyEnter" @keydown.tab="keyEnter" @keydown.up="keyUp" @keydown.down="keyDown">
        <div v-show="show" class="v-autocomplete-list">
             <div class="v-autocomplete-list-item" v-for="(item, i) in internalItems" @click="onClickItem(item)" v-bind:key="i" :class="{'v-autocomplete-item-active': i === cursor}" @mouseover="cursor = i">
                <div>{{ item }}</div>
             </div>
        </div>
      </div>
      <span class="input-group-btn">
          <button type="submit" class="btn btn-secondary" @click="goSearch"><span class="fa fa-solid fa-search"></span></button>
          <button type="button" class="btn btn-secondary" @click="goView"><span class="fa fa-solid fa-arrow-right"></span></button>
      </span>
    </div>
  </form>
</div>
</template>

<style scoped>
.v-autocomplete-list {
    position: absolute;
    z-index: 1000;
    border: 1px solid #CCC;
    background-color: #fff;
    width: 100%;
    top: 2rem;
    left: 0;
    border-bottom-left-radius: 12px;
    border-bottom-right-radius: 12px;
    box-shadow: 0 4px 5px rgba(0,0,0,0.15);
}

.thetree-dark-mode .v-autocomplete-list {
    background-color: #2d2f34;
    border: 1px solid #383b40;
}

.v-autocomplete-list-item {
    cursor: pointer;
    color: #373a3c;
    padding: 0.5rem;
    word-break: break-all;
    text-align: left;
    font-size: 0.9rem;
}

.thetree-dark-mode .v-autocomplete-list-item {
    color: #ddd;
}

.v-autocomplete-list-item.v-autocomplete-item-active {
    background-color: #f3f6fa;
}

.thetree-dark-mode .v-autocomplete-list-item.v-autocomplete-item-active {
    background-color: #383b40;
}
</style>

<script>
import AutocompleteMixin from '~/mixins/autocomplete';

export default {
    mixins: [AutocompleteMixin],
    methods: {
        goSearch() {
            if(!this.searchText) return;
            if (this.internalItems.includes(this.searchText)) {
                this.goView();
            } else {
                this.$router.push({ path: '/Search', query: { q: this.searchText } });
                this.$refs.input.blur();
            }
        },
        goView() {
            if(!this.searchText) return;
            this.$router.push(this.doc_action_link(this.searchText, 'w'));
            this.$refs.input.blur();
        },
        keyEnter(e) {
             if(this.show && this.cursor > -1 && this.internalItems[this.cursor]) {
                 e.preventDefault();
                 this.onSelectItem(this.internalItems[this.cursor]);
             } else {
                 this.goSearch();
                 this.showList = false;
             }
        },
        reset() {
            this.searchText = '';
            this.searchTextModel = '';
            this.items = [];
            this.internalItems = [];
        }
    },
    watch: {
        $route() {
            this.reset();
        }
    }
}
</script>

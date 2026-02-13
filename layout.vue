<template>
  <div class="Clue" :style="skinConfig">
    <div id="top"></div>
    <div class="nav-wrapper" :class="{ 'navbar-fixed-top': $store.state.localConfig['clue.fixed_navbar'] }">
      <nav class="navbar navbar-dark">
        <nuxt-link class="navbar-brand" to="/">{{ $store.state.config.logo_text }}</nuxt-link>
        <ul class="nav navbar-nav">
          <li class="nav-item">
            <nuxt-link class="nav-link" to="/RecentChanges"><span class="fa fa-solid fa-compass" style="font-size: 17px;"></span><span class="hide-title">최근 변경</span></nuxt-link>
          </li>
          <li class="nav-item">
            <nuxt-link class="nav-link" to="/RecentDiscuss"><span style="font-size:17px;" class="fa fa-solid fa-commenting"></span><span class="hide-title">최근 토론</span></nuxt-link>
          </li>
          <li class="nav-item">
            <div class="dropdown" :class="{ open: menuOpen }">
              <a class="nav-link dropdown-toggle dropdown-toggle-fix" href="#" @click.prevent="toggleMenu">
                <span class="fa fa-solid fa-bars"></span><span class="hide-title">메뉴</span>
              </a>
              <div class="dropdown-menu" role="menu" v-show="menuOpen">
                <div class="dr-admin" style="margin-top: 5px;"><span class="dr-admin fa fa-solid fa-wrench"></span><b>일반 도구</b></div>
                <div class="dropdown-divider"></div>
                <nuxt-link to="/Upload" class="dropdown-item"><span class="fa fa-solid fa-file-upload"></span> 파일 올리기</nuxt-link>
                <div class="dropdown-divider"></div>
                <nuxt-link to="/NeededPages" class="dropdown-item"><span class="fa fa-seedling fa-solid"></span> 작성이 필요한 문서</nuxt-link>
                <nuxt-link to="/OrphanedPages" class="dropdown-item"><span class="fa fa-solid fa-triangle-exclamation"></span> 고립된 문서</nuxt-link>
                <nuxt-link to="/OrphanedCategories" class="dropdown-item"><span class="fa fa-solid fa-triangle-exclamation"></span> 고립된 분류</nuxt-link>
                <nuxt-link to="/UncategorizedPages" class="dropdown-item"><span class="fa fa-list-check fa-solid"></span> 분류가 되지 않은 문서</nuxt-link>
                <nuxt-link to="/OldPages" class="dropdown-item"><span class="fa fa-hourglass-3 fa-solid"></span> 편집된 지 오래된 문서</nuxt-link>
                <nuxt-link to="/ShortestPages" class="dropdown-item"><span class="fa fa-solid fa-thumbs-down"></span> 내용이 짧은 문서</nuxt-link>
                <nuxt-link to="/LongestPages" class="dropdown-item"><span class="fa fa-solid fa-thumbs-up"></span> 내용이 긴 문서</nuxt-link>
                <nuxt-link to="/BlockHistory" class="dropdown-item"><span class="fa fa-ban fa-solid"></span> 차단 내역</nuxt-link>
                <nuxt-link to="/RandomPage" class="dropdown-item"><span class="fa fa-share fa-solid"></span> RandomPage</nuxt-link>
                <nuxt-link to="/License" class="dropdown-item"><span class="fa fa-copyright fa-solid"></span> 라이선스</nuxt-link>
                <template v-if="$store.state.session.menus.length">
                  <div class="dropdown-divider"></div>
                  <div class="dr-admin"><span class="dr-admin fa fa-hammer fa-solid"></span><b>권한 도구</b></div>
                  <div class="dropdown-divider"></div>
                  <template v-for="m in $store.state.session.menus">
                    <nuxt-link v-if="m.t === '권한'" :key="m.l + '-grant'" to="/admin/grant" class="dropdown-item"><span class="fa fa-solid fa-user-gear"></span> 권한</nuxt-link>
                    <nuxt-link v-else-if="m.t === '로그인 기록 조회'" :key="m.l + '-login-history'" to="/admin/login_history" class="dropdown-item"><span class="fa fa-solid fa-user-check"></span> 로그인 기록 조회</nuxt-link>
                    <nuxt-link v-else-if="m.t === 'ACL Group'" :key="m.l + '-acl-group'" to="/aclgroup" class="dropdown-item"><span class="fa fa-solid fa-user-minus"></span> ACL Group</nuxt-link>
                    <nuxt-link v-else-if="m.t === '일괄 되돌리기'" :key="m.l + '-batch-revert'" to="/admin/batch_revert" class="dropdown-item"><span class="fa fa-clock-rotate-left fa-solid"></span> 일괄 되돌리기</nuxt-link>
                    <nuxt-link v-else-if="m.t === '설정'" :key="m.l + '-config'" to="/admin/config" class="dropdown-item"><span class="fa fa-gear fa-solid"></span> 설정</nuxt-link>
                    <nuxt-link v-else-if="m.t === '개발자 설정'" :key="m.l + '-developer'" to="/admin/developer" class="dropdown-item"><span class="fa fa-code fa-solid"></span> 개발자 설정</nuxt-link>
                    <nuxt-link v-else :key="m.l + '-default'" :to="m.l" class="dropdown-item"><span class="fa fa-solid fa-crown"></span> {{ m.t }}</nuxt-link>
                  </template>
                </template>
              </div>
            </div>
          </li>
        </ul>
        <div class="navbar-login">
          <div class="dropdown login-menu" :class="{ open: loginMenuOpen }">
            <a id="login-menu" class="dropdown-toggle" type="button" @click="toggleLoginMenu">
              <img v-if="$store.state.session.gravatar_url" class="profile-img" :src="$store.state.session.gravatar_url">
              <span v-else class="fa fa-solid fa-user login-icon"></span>
            </a>
            <div class="dropdown-menu dropdown-menu-right login-dropdown-menu" v-show="loginMenuOpen">
              <div v-if="$store.state.session.account.type === UserTypes.Account" class="username dropdown-item">
                <b>{{ $store.state.session.account.name }}</b><br>사용자
              </div>
              <div v-else class="username dropdown-item">
                <b>{{ $store.state.session.account.name }}</b><br>IP 사용자
              </div>
              <div class="dropdown-divider"></div>
              <a href="#" class="dropdown-item" @click.prevent="openSettingModal"><span class="fa fa-solid fa-gear"></span> 설정</a>
              <a v-if="$store.state.currentTheme === 'light'" href="#" class="dropdown-item" @click.prevent="setLocalConfig('wiki.theme', 'dark')"><span class="fa fa-solid fa-moon"></span> 다크 테마로</a>
              <a v-if="$store.state.currentTheme === 'dark'" href="#" class="dropdown-item" @click.prevent="setLocalConfig('wiki.theme', 'light')"><span class="fa fa-solid fa-sun"></span> 라이트 테마로</a>
              <div class="dropdown-divider"></div>
              <template v-if="$store.state.session.account.type === UserTypes.Account">
                <nuxt-link to="/member/mypage" class="dropdown-item"><span class="fa fa-solid fa-user-gear"></span> 내 정보</nuxt-link>
                <nuxt-link :to="doc_action_link(user_doc($store.state.session.account.name), 'w')" class="dropdown-item"><span class="fa fa-solid fa-address-card"></span> 사용자 문서</nuxt-link>
                <nuxt-link to="/member/starred_documents" class="dropdown-item"><span class="fa fa-solid fa-folder-open"></span> 내 문서함</nuxt-link>
                <div class="dropdown-divider"></div>
              </template>
              <template v-if="$store.state.session.account.uuid">
                <nuxt-link class="dropdown-item" :to="contribution_link($store.state.session.account.uuid)"><span class="fa fa-solid fa-user-edit"></span> 내 문서 기여 목록</nuxt-link>
                <nuxt-link class="dropdown-item" :to="contribution_link_discuss($store.state.session.account.uuid)"><span class="fa fa-solid fa-comments"></span> 내 토론 기여 목록</nuxt-link>
                <nuxt-link class="dropdown-item" :to="contribution_link_edit_request($store.state.session.account.uuid)"><span class="fa fa-solid fa-code-pull-request"></span> 내 편집 요청 목록</nuxt-link>
                <div class="dropdown-divider"></div>
              </template>
              <nuxt-link v-if="$store.state.session.account.type === UserTypes.Account" to="/member/logout" data-add-redirect class="dropdown-item"><span class="fa fa-solid fa-sign-out"></span> 로그아웃</nuxt-link>
              <nuxt-link v-else to="/member/login" data-add-redirect class="dropdown-item"><span class="fa fa-solid fa-sign-in"></span> 로그인</nuxt-link>
            </div>
          </div>
        </div>
        <search-form />
      </nav>
    </div>
    <div class="content-wrapper" :class="{ 'hide-sidebar': $store.state.localConfig['clue.sidebar'] === 'hide' || $store.state.localConfig['clue.sidebar'] === 'footer' }">
      <div class="clue-sidebar">
        <div class="clue-right-fixed" :class="{ 'fixed': $store.state.localConfig['clue.sidebar'] === 'fix' }">
          <div class="live-recent">
            <div class="live-recent-header">
              <ul class="nav nav-tabs">
                <li class="nav-item" style="text-align: left;">
                  <div class="recent-head-title">
                    <h3 style="margin: 0; display: flex; align-items: center;">
                      <span class="fa fa-solid fa-compass"></span>
                      <span class="recent-head-text">최근 변경</span>
                    </h3>
                    <nuxt-link to="/RecentChanges" class="btn btn-sm btn-primary btn-recentchanges">더보기</nuxt-link>
                  </div>
                </li>
              </ul>
            </div>
            <recent-card :limit="13" />
          </div>
        </div>
      </div>
      <div id="content" class="container-fluid clue-content">
            <div v-if="$store.state.config['wiki.sitenotice']" id="site-notice" class="notification">
                    <span class="label" v-html="$store.state.config['wiki.sitenotice']" @click="onDynamicContentClick($event)" />
            </div>
        <div class="clue-content-header">
          <content-tool />
          <div class="title">
            <h1 v-if="$store.state.page.data.document && $store.state.page.viewName !== 'error'" style="margin-bottom: 0;">
              <nuxt-link :to="doc_action_link($store.state.page.data.document, 'w')">
                <span v-if="$store.state.page.data.document.forceShowNamespace !== false" class="namespace">{{$store.state.page.data.document.namespace}}</span> {{$store.state.page.data.document.title}}
              </nuxt-link>
              <small v-if="$store.state.page.viewName === 'edit_edit_request' || $store.state.page.viewName === 'edit_request'">(편집 요청)</small>
              <small v-else-if="$store.state.page.viewName === 'edit' && $store.state.page.data.body.section">(r{{$store.state.page.data.body.baserev}} 문단 편집)</small>
              <small v-else-if="$store.state.page.viewName === 'edit' && $store.state.page.data.body.baserev === '0'">(새 문서 생성)</small>
              <small v-else-if="$store.state.page.viewName === 'edit'">(r{{$store.state.page.data.body.baserev}} 편집)</small>
              <small v-else-if="$store.state.page.viewName === 'history'">(역사)</small>
              <small v-else-if="$store.state.page.viewName === 'backlink'">(역링크)</small>
              <small v-else-if="$store.state.page.viewName === 'move'">(이동)</small>
              <small v-else-if="$store.state.page.viewName === 'delete'">(삭제)</small>
              <small v-else-if="$store.state.page.viewName === 'acl'">(ACL)</small>
              <small v-else-if="$store.state.page.viewName === 'thread'">(토론)</small>
              <small v-else-if="$store.state.page.viewName === 'thread_list'">(토론 목록)</small>
              <small v-else-if="$store.state.page.viewName === 'thread_list_close'">(닫힌 토론)</small>
              <small v-else-if="$store.state.page.viewName === 'edit_request_close'">(닫힌 편집 요청)</small>
              <small v-else-if="$store.state.page.viewName === 'diff'">(비교)</small>
              <small v-else-if="$store.state.page.viewName === 'revert' && $store.state.page.data.rev">(r{{$store.state.page.data.rev}}로 되돌리기)</small>
              <small v-else-if="$store.state.page.viewName === 'raw' && $store.state.page.data.rev">(r{{$store.state.page.data.rev}} RAW)</small>
              <small v-else-if="$store.state.page.viewName === 'blame' && $store.state.page.data.rev">(r{{$store.state.page.data.rev}} Blame)</small>
              <small v-else-if="$store.state.page.viewName === 'wiki' && $store.state.page.data.rev">(r{{$store.state.page.data.rev}} 판)</small>
            </h1>
            <h1 v-else style="margin-bottom: 0;">{{ $store.state.page.title }}</h1>
            <div v-if="$store.state.page.data.date" style="font-size: 0.8rem; margin-top: 0.2rem;">
                마지막 수정: <local-date :date="$store.state.page.data.date" />
            </div>
          </div>
        </div>
        <div class="clue-content-main wiki-article">
            <div style="margin-top: 10px;"></div>
            <div v-if="$store.state.viewData.errorAlert" class="alert alert-danger" role="alert">
                <span v-html="$store.state.viewData.errorAlert"></span>
                <span style="display:none">{{$store.state.viewData.errorAlertExists = true}}</span>
            </div>
            
            <div v-if="$store.state.session.user_document_discuss && $store.state.localConfig['wiki.hide_user_document_discuss'] !== $store.state.session.user_document_discuss" class="alert alert-primary alert-dismissible fade show" role="alert">
                 현재 진행 중인 <nuxt-link :to="doc_action_link(user_doc($store.state.session.account.name), 'discuss')">사용자 토론</nuxt-link>이 있습니다.
                 <button type="button" class="close" @click="$store.commit('localConfigSetValue', {key: 'wiki.hide_user_document_discuss', value: $store.state.session.user_document_discuss})">
                    <span aria-hidden="true">&times;</span>
                 </button>
            </div>

            <div v-if="$store.state.page.viewName === 'notfound' && $store.state.page.data.document.namespace === '문서'" class="alert alert-danger" style="line-height: 2.1rem;">
                '{{ $store.state.page.title }}'을(를) 검색하시겠습니까?
                 <div class="float-right"><a class="btn btn-danger btn-sm" :href="'/Search?q='+ $store.state.page.title">검색</a></div>
                 <div class="clearfix"></div>
            </div>

           <nuxt />
        </div>

        <div
          v-if="isFooterRecentVisible"
          class="footer-recent"
        >
          <div class="live-recent-header">
            <ul class="nav nav-tabs">
              <li class="nav-item" style="text-align: left;width: 100%;">
                <div class="recent-head-title">
                  <h3 style="margin: 0; display: flex; align-items: center;">
                    <span class="fa fa-solid fa-compass"></span>
                    <span class="recent-head-text">최근 변경</span>
                  </h3>
                  <nuxt-link to="/RecentChanges" class="btn btn-sm btn-primary btn-recentchanges">더보기</nuxt-link>
                </div>
              </li>
            </ul>
          </div>
          <recent-card :limit="10" />
        </div>

        <div id="bottom" class="clue-footer">
          <ul v-if="$store.state.page.viewName === 'wiki'" class="footer-info">
              <li class="footer-info-copyright" v-html="$store.state.page.data.copyright_text"></li>
          </ul>
          <ul class="footer-places" v-if="$store.state.config.footer_html" v-html="$store.state.config.footer_html"></ul>
          <ul class="footer-places" v-else></ul>
          <ul class="footer-icons">
            <li class="footer-poweredbyico">
              <a href="//github.com/blindplus/thetree-skin-newclue">newclue</a> |
              <a href="//github.com/wjdgustn/thetree">the tree</a>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
  <div class="scroll-buttons">
    <a class="scroll-toc" href="#toc"><i class="fa fa-solid fa-list-1-2" aria-hidden="true"></i></a>
    <a id="left" class="scroll-button" href="#top"><i class="fa fa-solid fa-arrow-up" aria-hidden="true"></i></a>
    <a id="right" class="scroll-bottom" href="#bottom"><i class="fa fa-solid fa-arrow-down" aria-hidden="true"></i></a>
  </div>
</template>

<style>
  @import "./css/bootstrap.min.css";
  @import "https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.7.2/css/all.min.css";
  @import "https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/static/pretendard.min.css";
  @import "./css/default.css";
  @import './css/default_mobile.css';

  @import "./css/layouts/contentTool.css";
  @import "./css/layouts/recentCard.css";
  @import "./css/layouts/searchForm.css";

  @import "./css/dark.css";
</style>

<script>
import Common from '~/mixins/common';

import RecentCard from './layouts/recentCard.vue';
import SearchForm from './layouts/searchForm.vue';
import ContentTool from './layouts/contentTool.vue';
import LocalDate from '../../src/components/localDate.vue';

export default {
  mixins: [Common],
  components: {
    RecentCard, SearchForm, ContentTool, LocalDate
  },
  data() {
    return {
      menuOpen: false,
      loginMenuOpen: false,
      isShowACLMessage: true,
      isFooterRecentVisible: false,
      UserTypes: {
        Account: 1,
      }
    };
  },
  computed: {
    sidebarSetting() {
      return this.$store.state.localConfig['clue.sidebar'];
    },
    currentTheme() {
      return this.$store.state.currentTheme;
    },
    editAclMessageHtml() {
      const page = this.$store.state.page;
      const message = page?.data?.edit_acl_message;
      if (!message) return '';
      let text = '';
      if (typeof document !== 'undefined') {
        const container = document.createElement('div');
        container.innerHTML = message;
        text = (container.textContent || container.innerText || '').trim();
      }
      if (!text) {
        text = message
          .replace(/<br\s*\/?>/gi, '')
          .replace(/&nbsp;/gi, ' ')
          .replace(/<[^>]+>/g, '')
          .trim();
      }
      return text.length > 0 ? message : '';
    },
    hasEditAclMessage() {
      return this.editAclMessageHtml.length > 0;
    },
    requestable() {
      const page = this.$store.state.page;
      return page?.data?.editable === true && this.hasEditAclMessage && page?.viewName !== 'notfound';
    },
    brand_color() {
        return this.selectByTheme(this.$store.state.config['skin.newclue.brand_color_1'] ?? '#4188f1', '#2d2f34');
    },
    skinConfig() {
        return {
            '--clue-brand-color': this.brand_color,
            '--clue-brand-dark-color': this.selectByTheme(this.$store.state.config['skin.newclue.brand_dark_color_1'] ?? this.darkenColor(this.brand_color), '#16171a'),
            '--clue-brand-bright-color': this.selectByTheme(this.$store.state.config['skin.newclue.brand_bright_color_1'] ?? this.lightenColor(this.brand_color), '#383b40'),
            '--clue-navbar-logo-image': this.$store.state.config['skin.newclue.navbar_logo_image'] || (this.$store.state.config['wiki.logo_url'] && `url(${this.$store.state.config['wiki.logo_url']})`),
            '--clue-navbar-logo-minimum-width': this.$store.state.config['skin.newclue.navbar_logo_minimum_width'],
            '--clue-navbar-logo-width': this.$store.state.config['skin.newclue.navbar_logo_width'],
            '--clue-navbar-logo-size': this.$store.state.config['skin.newclue.navbar_logo_size'],
            '--clue-navbar-logo-padding': this.$store.state.config['skin.newclue.navbar_logo_padding'],
            '--clue-navbar-logo-margin': this.$store.state.config['skin.newclue.navbar_logo_margin'],
            '--brand-color-1': 'var(--clue-brand-color)',
            '--brand-color-2': this.selectByTheme(this.$store.state.config['skin.newclue.brand_color_2'] ?? 'var(--clue-brand-color)', 'var(--clue-brand-color)'),
            '--brand-bright-color-1': 'var(--clue-brand-bright-color)',
            '--brand-bright-color-2': this.selectByTheme(this.$store.state.config['skin.newclue.brand_bright_color_2'] ?? 'var(--clue-brand-bright-color)', 'var(--clue-brand-bright-color)'),
            '--text-color': this.selectByTheme('#373a3c', '#ddd'),
            '--article-background-color': this.selectByTheme('#fff', '#1d2023'),
        };
    }
  },
  watch: {
    sidebarSetting() {
      if (typeof window !== 'undefined') {
        this.$nextTick(() => this.updateFooterRecentVisibility());
      }
    },
    currentTheme: {
        immediate: true,
        handler(newTheme) {
            if (typeof document !== 'undefined') {
                if(newTheme === 'dark') {
                    document.body.classList.add('thetree-dark-mode');
                } else {
                    document.body.classList.remove('thetree-dark-mode');
                }
            }
        }
    }
  },
  methods: {
    toggleMenu() {
      this.menuOpen = !this.menuOpen;
      if (this.menuOpen) this.loginMenuOpen = false;
    },
    toggleLoginMenu() {
      this.loginMenuOpen = !this.loginMenuOpen;
      if (this.loginMenuOpen) this.menuOpen = false;
    },
    openSettingModal() {
      this.$store.commit('openSettingModal');
    },
    setLocalConfig(key, value) {
      this.$store.commit('localConfigSetValue', { key, value });
    },
    onDocumentClick(e) {
      if (this.menuOpen && !e.target.closest('.dropdown')) {
        this.menuOpen = false;
      }
      if (this.loginMenuOpen && !e.target.closest('.login-menu')) {
        this.loginMenuOpen = false;
      }
      if ((this.menuOpen || this.loginMenuOpen) && e.target.closest('.dropdown-menu a')) {
        this.menuOpen = false;
        this.loginMenuOpen = false;
      }
    },
    updateFooterRecentVisibility() {
      if (typeof window === 'undefined') return;
      const width = window.innerWidth || 0;
      const sidebarSetting = this.sidebarSetting;
      if (width <= 768) {
        this.isFooterRecentVisible = sidebarSetting !== 'hide';
      } else {
        this.isFooterRecentVisible = sidebarSetting === 'footer';
      }
    },
    darkenColor(hex, percent=50) {
        let r = parseInt(hex.substring(1, 3), 16);
        let g = parseInt(hex.substring(3, 5), 16);
        let b = parseInt(hex.substring(5, 7), 16);

        r = Math.round(r * (1 - percent / 100));
        g = Math.round(g * (1 - percent / 100));
        b = Math.round(b * (1 - percent / 100));

        return "#" + ((r < 16 ? "0" : "") + r.toString(16)) + ((g < 16 ? "0" : "") + g.toString(16)) + ((b < 16 ? "0" : "") + b.toString(16));
    },
    lightenColor(hex, percent=50) {
        let r = parseInt(hex.substring(1, 3), 16);
        let g = parseInt(hex.substring(3, 5), 16);
        let b = parseInt(hex.substring(5, 7), 16);

        r = Math.round(r + (255 - r) * (percent / 100));
        g = Math.round(g + (255 - g) * (percent / 100));
        b = Math.round(b + (255 - b) * (percent / 100));

        return "#" + ((r < 16 ? "0" : "") + r.toString(16)) + ((g < 16 ? "0" : "") + g.toString(16)) + ((b < 16 ? "0" : "") + b.toString(16));
    },
    selectByTheme(light, dark) {
        return this.$store.state.currentTheme === 'dark' ? dark : light;
    }
  },
  mounted() {
    document.addEventListener('click', this.onDocumentClick);
    if (typeof window !== 'undefined') {
      window.addEventListener('resize', this.updateFooterRecentVisibility);
      this.updateFooterRecentVisibility();
    }
  },
  beforeUnmount() {
    document.removeEventListener('click', this.onDocumentClick);
    if (typeof window !== 'undefined') {
      window.removeEventListener('resize', this.updateFooterRecentVisibility);
    }
  }
}
</script>

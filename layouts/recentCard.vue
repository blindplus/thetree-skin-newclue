<template>
  <div class="recent-card">
    <div class="live-recent-content" style="border-bottom-right-radius: 12px;border-bottom-left-radius: 12px;">
      <ul class="live-recent-list" id="live-recent-list" style="padding-top: 10px;">
        <template v-if="!recent || recent.length === 0">
           <li v-for="n in 15" :key="n"><span class="recent-item">&nbsp;</span></li>
        </template>
        <template v-else>
          <li v-for="(r, i) in recent.slice(0, limit)" :key="i">
            <nuxt-link class="recent-item" :class="{ 'removed': r.status === 'delete' }" :to="doc_action_link(r.document, 'w')" @click.native="$store.state.aClickHandler">
              <span :class="{ 'removed-recent': r.status === 'delete' }" class="recent-item-text">{{ r.document && r.document.title ? r.document.title : r.document }}</span>
              <span class="recent-item-date"><local-date :date="new Date(r.date * 1000)" type="relative" /></span>
            </nuxt-link>
          </li>
        </template>
      </ul>
    </div>
  </div>
</template>

<script>
import RecentCardMixin from '~/mixins/recentCard';

export default {
    mixins: [RecentCardMixin],
    props: {
        limit: {
            type: Number,
            default: 15
        }
    }
}
</script>

<template>
  <router-link
    :to="menuItem.toState"
    tag="li"
    active-class="active"
    :class="computedClass"
  >
    <a
      :class="getMenuItemClass"
      data-tooltip
      aria-haspopup="true"
      :title="menuItem.toolTip"
    >
      <i :class="menuItem.icon" />
      {{ menuItem.label }}
      <span
        v-if="showItem(menuItem)"
        class="ion-ios-plus-outline"
        @click.prevent="newLinkClick"
      />
    </a>
    <ul v-if="menuItem.hasSubMenu" class="nested vertical menu">
      <router-link
        v-for="child in menuItem.children"
        :key="child.label"
        active-class="active flex-container"
        :class="computedInboxClass(child)"
        tag="li"
        :to="child.toState"
      >
        <a>{{ child.label }}</a>
      </router-link>
    </ul>
  </router-link>
</template>

<script>
/* eslint no-console: 0 */
import { mapGetters } from 'vuex';

import router from '../../routes';
import auth from '../../api/auth';

export default {
  props: {
    menuItem: {
      type: Object,
      default() {
        return {};
      },
    },
  },
  computed: {
    ...mapGetters({
      activeInbox: 'getSelectedInbox',
    }),
    getMenuItemClass() {
      return this.menuItem.cssClass
        ? `side-menu ${this.menuItem.cssClass}`
        : 'side-menu';
    },
    computedClass() {
      // If active Inbox is present
      // donot highlight conversations
      if (this.activeInbox) return ' ';

      if (
        this.$store.state.route.name === 'inbox_conversation' &&
        this.menuItem.toStateName === 'home'
      ) {
        return 'active';
      }
      return ' ';
    },
  },
  methods: {
    computedInboxClass(child) {
      if (parseInt(this.activeInbox, 10) === child.channel_id) {
        return 'active flex-container';
      }
      return ' ';
    },
    newLinkClick() {
      router.push({ name: 'settings_inbox_new', params: { page: 'new' } });
    },
    showItem(item) {
      return auth.isAdmin() && item.newLink !== undefined;
    },
  },
};
</script>

<template>
  <header class="navbar" :class="{'navbar-down': shouldShowStatusBar}">
    <div class="navbar-container">
      <SidebarButton @toggle-sidebar="$emit('toggle-sidebar')" />

      <RouterLink :to="$localePath" class="home-link">
        <img
          v-if="$site.themeConfig.logo"
          class="logo"
          :src="$withBase($site.themeConfig.logo)"
          :alt="$siteTitle"
        />
        <span
          v-if="$siteTitle"
          ref="siteName"
          class="site-name"
          :class="{ 'can-hide': $site.themeConfig.logo }"
          >{{ $siteTitle }}</span
        >
      </RouterLink>

      <div
        class="links"
        :style="
          linksWrapMaxWidth
            ? {
                'max-width': linksWrapMaxWidth + 'px',
              }
            : {}
        "
      >
        <AlgoliaSearchBox v-if="isAlgoliaSearch" :options="algolia" />
        <SearchBox
          v-else-if="
            $site.themeConfig.search !== false &&
            $page.frontmatter.search !== false
          "
        />
        <NavLinks class="can-hide" />
      </div>
    </div>
  </header>
</template>

<script>
import AlgoliaSearchBox from "@AlgoliaSearchBox";
import SearchBox from "@SearchBox";
import SidebarButton from "@theme/components/SidebarButton.vue";
import NavLinks from "@theme/components/NavLinks.vue";

export default {
  name: "Navbar",

  components: {
    SidebarButton,
    NavLinks,
    SearchBox,
    AlgoliaSearchBox,
  },

  props:['shouldShowStatusBar'],

  data() {
    return {
      linksWrapMaxWidth: null,
    };
  },

  computed: {
    algolia() {
      return (
        this.$themeLocaleConfig.algolia || this.$site.themeConfig.algolia || {}
      );
    },

    isAlgoliaSearch() {
      return this.algolia && this.algolia.apiKey && this.algolia.indexName;
    },
  },

  mounted() {
    const MOBILE_DESKTOP_BREAKPOINT = 719; // refer to config.styl
    const NAVBAR_VERTICAL_PADDING =
      parseInt(css(this.$el, "paddingLeft")) +
      parseInt(css(this.$el, "paddingRight"));
    const handleLinksWrapWidth = () => {
      if (document.documentElement.clientWidth < MOBILE_DESKTOP_BREAKPOINT) {
        this.linksWrapMaxWidth = null;
      } else {
        this.linksWrapMaxWidth =
          this.$el.offsetWidth -
          NAVBAR_VERTICAL_PADDING -
          ((this.$refs.siteName && this.$refs.siteName.offsetWidth) || 0);
      }
    };
    handleLinksWrapWidth();
    window.addEventListener("resize", handleLinksWrapWidth, false);
  },
};

function css(el, property) {
  // NOTE: Known bug, will return 'auto' if style value is 'auto'
  const win = el.ownerDocument.defaultView;
  // null means not to return pseudo styles
  return win.getComputedStyle(el, null)[property];
}
</script>

<style lang="stylus">

.navbar {
  background-color: var(--vp-c-bg);
  transition: background-color 0.5s;
  position: fixed;
  z-index: 20;
  top: 0;
  left: 0;
  right: 0;
  height: $navbarHeight;
  box-sizing: border-box;
  border-bottom: 1px solid var(--vp-c-divider-light);

  .navbar-container {
    height: 100%;
    max-width: var(--vp-screen-max-width);
    margin: 0 auto;
    display: flex;
  }

  .home-link {
    height: 100%;
    display: flex;
    align-items: center;
    z-index: 1;
    background-color: var(--vp-c-bg);
    padding-right: 1rem;
  }

  a, span, img {
    display: inline-block;
  }

  .logo {
    height: 1.4rem;
    min-width: 1.4rem;
    margin-right: 0.2rem;
  }

  .site-name {
    font-size: 22px;
    font-weight: 500;
    color: var(--vp-c-text-1);
    position: relative;
  }

  .links {
    height: 100%;
    padding-left: 1.5rem;
    box-sizing: border-box;
    white-space: nowrap;
    font-size: 13px;
    display: flex;
    align-items: center;
    justify-content: flex-end;
    flex: 1;

    .search-box {
      flex: 0 0 auto;
      vertical-align: top;
    }
  }
}

@media (max-width: $MQMobile) {
  .navbar {
    .navbar-container {
      max-width: 100%;
    }

    .can-hide {
      display: none;
    }

    .links {
      padding-left: 1.5rem;
    }

    .site-name {
      width: calc(100vw - 9.4rem);
      overflow: hidden;
      white-space: nowrap;
      text-overflow: ellipsis;
    }
  }
}

.navbar-down{
  top: var(--vp-statusbar-height);
  border-top: 1px solid var(--vp-c-divider-light);
}
</style>

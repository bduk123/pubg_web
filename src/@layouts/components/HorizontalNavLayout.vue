<script setup>
import UserProfile from '@/layouts/components/UserProfile.vue';
import { HorizontalNav } from '@layouts/components';
import { themeConfig } from '@themeConfig';
// ℹ️ Using import from `@layouts` causing build to hangup

// import { useLayouts } from '@layouts'
import { useLayoutConfigStore } from '@layouts/stores/config';

const props = defineProps({
  navItems: {
    type: null,
    required: true,
  },
})
const router = useRouter()
const shallShowPageLoading = ref(false)
const isIndexRoute = computed(() => router.currentRoute.value.path === '/')
router.beforeEach(() => {
  shallShowPageLoading.value = true
})
router.afterEach(() => {
  shallShowPageLoading.value = false
})
const configStore = useLayoutConfigStore()
</script>

<template>
  <div
    class="layout-wrapper"
    data-allow-mismatch
    :class="configStore._layoutClasses"
  >
    <div
      class="layout-navbar-and-nav-container"
      :class="configStore.isNavbarBlurEnabled && 'header-blur'"
    >
      <!-- 👉 Navbar -->
      <!-- <div class="layout-navbar">
        <div class="navbar-content-container">
          <slot name="navbar" />
        </div>
      </div> -->
      <!-- 👉 Navigation -->
      <div class="layout-horizontal-nav">
        
        <div class="horizontal-nav-content-container d-flex justify-between align-items-end w-100">
        <!-- Left Side -->
        <div class="d-flex align-items-end gap-4 flex-wrap">
          <h1 class="app-title font-weight-bold leading-normal text-xl text-capitalize mb-2">
            {{ themeConfig.app.title }}
          </h1>
        </div>
        <div class="d-flex justify-center flex-grow-1">
          <HorizontalNav :nav-items="navItems" />
        </div>
        <!-- Right Side -->
        <div class="d-flex align-items-end gap-2">
          <!-- <NavbarThemeSwitcher /> -->
          <UserProfile />
        </div>
      </div>
      </div>
    </div>

    <template v-if="!isIndexRoute">
      <main class="layout-page-content">
        <template v-if="$slots['content-loading']">
          <template v-if="shallShowPageLoading">
            <slot name="content-loading" />
          </template>
          <template v-else>
            <slot />
          </template>
        </template>
        <template v-else>
          <slot />
        </template>
      </main>
    </template>

    <template v-else>
      
      <slot />
    </template>

    <!-- 👉 Footer -->
    <footer class="layout-footer">
      <div class="footer-content-container">
        <slot name="footer" />
      </div>
    </footer>
  </div>
</template>

<style lang="scss">
@use "@configured-variables" as variables;
@use "@layouts/styles/placeholders";
@use "@layouts/styles/mixins";

.layout-wrapper {
  &.layout-nav-type-horizontal {
    display: flex;
    flex-direction: column;

    // // TODO(v2): Check why we need height in vertical nav & min-height in horizontal nav
    // min-height: 100%;
    min-block-size: 100dvh;

    .layout-navbar-and-nav-container {
      z-index: 1;
    }

    .layout-navbar {
      z-index: variables.$layout-horizontal-nav-layout-navbar-z-index;
      block-size: variables.$layout-horizontal-nav-navbar-height;

      // ℹ️ For now we are not independently managing navbar and horizontal nav so we won't use below style to avoid conflicting with combo style of navbar and horizontal nav
      // If we add independent style of navbar & horizontal nav then we have to add :not for avoiding conflict with combo styles
      // .layout-navbar-sticky & {
      //   @extend %layout-navbar-sticky;
      // }

      // ℹ️ For now we are not independently managing navbar and horizontal nav so we won't use below style to avoid conflicting with combo style of navbar and horizontal nav
      // If we add independent style of navbar & horizontal nav then we have to add :not for avoiding conflict with combo styles
      // .layout-navbar-hidden & {
      //   @extend %layout-navbar-hidden;
      // }
    }

    // 👉 Navbar
    .navbar-content-container {
      @include mixins.boxed-content;
    }

    // 👉   Content height fixed
    &.layout-content-height-fixed {
      max-block-size: 100dvh;

      .layout-page-content {
        overflow: hidden;

        > :first-child {
          max-block-size: 100%;
          overflow-y: auto;
        }
      }
    }

    // 👉 Footer
    // Boxed content
    .layout-footer {
      .footer-content-container {
        @include mixins.boxed-content;
      }
    }
  }

  // If both navbar & horizontal nav sticky
  &.layout-navbar-sticky.horizontal-nav-sticky {
    .layout-navbar-and-nav-container {
      position: sticky;
      inset-block-start: 0;
      will-change: transform;
    }
  }

  &.layout-navbar-hidden.horizontal-nav-hidden {
    .layout-navbar-and-nav-container {
      display: none;
    }
  }
}

// 👉 Horizontal nav nav
.layout-horizontal-nav {
  z-index: variables.$layout-horizontal-nav-z-index;

  // .horizontal-nav-sticky & {
  //   width: 100%;
  //   will-change: transform;
  //   position: sticky;
  //   top: 0;
  // }

  // .horizontal-nav-hidden & {
  //   display: none;
  // }

  .horizontal-nav-content-container {
    @include mixins.boxed-content(true);
  }
}
</style>

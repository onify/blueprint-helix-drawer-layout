/**
* This is a exmple of a Onify Helix custom layout.
* It includes the following main sections:
*
* - User Settings Dialog: A dialog for user settings, which includes a title, a user settings component, and
close/success event handlers. This is a Onify Helix component.
* - App Bar: A top navigation bar with a title, search button, notification button, and user settings menu.
* - Navigation Drawer: A side navigation drawer with a list of menu items and a settings link.
* - Main Content: A slot for the main content of the application.
*
* The component uses the following external dependencies:
* - `@vueuse/core` for the `useToggle` composable.
* - `@onify/helix-core` for the `useHConfig` composable. Using Onify Helix core package.
* - `@onify/helix-pages` for the `HUserSettings` component. Using Onify Helix pages package
*
* Reactive properties:
* - `drawer`: Controls the visibility of the navigation drawer.
* - `isShowingUserSettings`: Controls the visibility of the user settings dialog.
*
* Methods:
* - `toggleUserSettings`: Toggles the visibility of the user settings dialog.
* - `onLogout`: Handles the logout process by redirecting to the logout URL with the current URL as a redirect
parameter.
*
* Data:
* - `oUser`: Contains user information such as name and email. This Onify specific user data.
* - `config`: Contains configuration settings.
* - `items`: An array of menu items for the navigation drawer.
*/
<template>
  <v-layout>
    <!-- User settings dialog -->
    <v-dialog v-model="isShowingUserSettings" width="500px">
      <v-card class="user-settings">
        <v-card-title>{{ $t('user_settings') }}</v-card-title>
        <v-card-text>
          <h-user-settings @close="toggleUserSettings(false)" @success="toggleUserSettings(close)"></h-user-settings>
        </v-card-text>
      </v-card>
    </v-dialog>
    <!-- User settings dialog -->
    <v-app-bar border="b" class="ps-4" flat>
      <v-app-bar-nav-icon v-if="$vuetify.display.smAndDown" @click="drawer = !drawer" />

      <v-app-bar-title>Application</v-app-bar-title>

      <template #append>
        <div class="d-flex ga-2 align-center">
          <v-btn color="medium-emphasis" icon="mdi:mdi-magnify" />
          <v-btn color="medium-emphasis" icon="mdi:mdi-bell-outline" />
          <v-divider class="align-self-center" length="24" vertical />
          <!-- User settings menu -->
          <v-menu min-width="200px" location="bottom">
            <template #activator="{ props }">
              <v-btn icon="mdi:mdi-account" v-bind="props" color="primary" aria-label="account"></v-btn>
            </template>
            <v-card>
              <v-card-text>
                <div class="mx-auto text-center">
                  <v-avatar color="primary">
                    <span class="text-h5">{{ oUser?.name[0] }}</span>
                  </v-avatar>
                  <h3>{{ oUser?.name }}</h3>
                  <p class="text-caption mt-1">
                    {{ oUser?.email }}
                  </p>
                  <v-divider class="my-3"></v-divider>
                  <slot name="prepend-menu-actions"></slot>
                  <v-btn variant="text" @click="toggleUserSettings(true)">{{ $t('settings') }}</v-btn>
                  <v-divider class="my-3"></v-divider>
                  <v-btn variant="text" @click="onLogout()">{{ $t('logout') }}</v-btn>
                  <slot name="append-menu-actions"></slot>
                </div>
              </v-card-text>
            </v-card>
          </v-menu>
          <!-- User settings menu -->
        </div>
      </template>
    </v-app-bar>
    <v-navigation-drawer v-model="drawer" color="surface-variant">
      <v-list density="compact" item-props :items="items" nav />
      <template #append>
        <v-list-item class="ma-2" link nav prepend-icon="mdi:mdi-cog-outline" title="Settings" />
      </template>
    </v-navigation-drawer>
    <v-main>
      <div>
        <slot></slot>
      </div>
    </v-main>
  </v-layout>
</template>

<script setup>
import { useToggle } from '@vueuse/core';
import { useHConfig } from '@onify/helix-core';
import { HUserSettings } from '@onify/helix-pages';

const { oUser } = useOCommon(); // Needed to get user information
const config = useHConfig(); // Needed to load configuration

const drawer = ref(true);

// User Settings Dialog toggle
const isShowingUserSettings = ref(false);
const toggleUserSettings = useToggle(isShowingUserSettings);

// Handle logout
function onLogout() {
  const redirectUrl = window.location.href;
  window.location.href = `${config.logoutUrl}?redirect=${redirectUrl}`;
}

// Menu items
const items = ref([
  {
    title: 'Dashboard',
    prependIcon: 'mdi:mdi-view-dashboard-outline',
    link: true,
  },
  {
    title: 'Team',
    prependIcon: 'mdi:mdi-account-group',
    link: true,
  },
  {
    title: 'Projects',
    prependIcon: 'mdi:mdi-briefcase-outline',
    link: true,
  },
  {
    title: 'Calendar',
    prependIcon: 'mdi:mdi-calendar',
    link: true,
  },
  {
    title: 'Reports',
    prependIcon: 'mdi:mdi-file-chart-outline',
    link: true,
  },
]);
</script>

<template>

  <v-bottom-navigation
      v-if="mobile"
      :grow=true
  >
    <v-btn
        v-for="item in menuItems"
        :key="item.value"
        :value="item.value"
        @click="navigateTo(item.value)"
    >
      <v-icon
          class="navbar-icon"
      >
        {{ item.icon }}
      </v-icon>
    </v-btn>
  </v-bottom-navigation>

  <v-navigation-drawer
      v-else
      expand-on-hover
      :rail=true
      class="sticky-navbar"
  >
    <v-list
        :nav=true
    >
      <v-list-item
          prepend-avatar="https://w7.pngwing.com/pngs/889/451/png-transparent-baden-wuerttemberg-cooperative-state-university-mosbach-baden-wurttemberg-cooperative-state-university-baden-wuerttemberg-cooperative-state-university-loerrach-baden-wuerttemberg-cooperative.png"
          title="Digital Blackboard"
          subtitle="Mehr brauchst du nicht!"
      ></v-list-item>

      <v-divider></v-divider>

      <v-list-item
          v-for="item in menuItems"
          :key="item.value"
          :to="item.value"
          :prepend-icon="item.icon"
          :title="item.title"
      ></v-list-item>

    </v-list>

  </v-navigation-drawer>

</template>

<script setup>
import {useDisplay} from "vuetify";

const {mobile} = useDisplay()
</script>

<script>
export default {

  data() {
    return {

      menuItems: [
        {
          icon: 'mdi-bulletin-board', title: 'Blackboard', value: '/'
        },
        {
          icon: 'mdi-domain', title: 'Dual Living', value: 'dualliving'
        },
        {
          icon: 'mdi-calendar-clock', title: 'Events & Infos', value: 'events'
        },
        {
          icon: 'mdi-school', title: 'StudyHub', value: 'studyhub'
        },
        {
          icon: 'mdi-account', title: 'Account', value: 'account'
        },
      ],

    };

  },
  methods: {
    /**
     * Navigates to a specified route using Vue Router.
     *
     * @method
     * @param {string} itemValue - The path of the route to navigate to.
     */
    navigateTo(itemValue) {
      // Check if itemValue is provided
      if (itemValue) {
        // Use Vue Router to navigate to the specified route
        this.$router.push({path: itemValue});
      }
    },
  },
};

</script>

<style>

</style>
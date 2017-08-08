<template>
  <v-app id="app" dark standalone>
    <v-navigation-drawer temporary v-model="drawer" :mini-variant="mini" dark overflow absolute>
      <v-list class="pa-0">
        <v-list-tile v-if="mini" @click.native.stop="mini = !mini">
          <v-list-tile-action>
            <v-icon light>chevron_right</v-icon>
          </v-list-tile-action>
        </v-list-tile>
        <v-list-tile avatar tag="div">
          <v-list-tile-avatar>
            <img src="./assets/movie-icon.png" />
          </v-list-tile-avatar>
          <v-list-tile-content>
            <v-list-tile-title>Menu</v-list-tile-title>
          </v-list-tile-content>
          <v-list-tile-action>
            <v-btn icon @click.native.stop="mini = !mini">
              <v-icon>chevron_left</v-icon>
            </v-btn>
          </v-list-tile-action>
        </v-list-tile>
      </v-list>
      <v-list class="pt-0" dense>
        <v-divider light></v-divider>
        <v-list-tile v-for="item in items" :key="item.title" :to="{ name: item.title }">
          <v-list-tile-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-tile-action>
          <v-list-tile-content>
            <v-list-tile-title>{{ item.title }}</v-list-tile-title>
          </v-list-tile-content>
        </v-list-tile>
      </v-list>
    </v-navigation-drawer>
    <v-toolbar fixed dark>
      <v-toolbar-title>Movie Search</v-toolbar-title>

      <v-spacer></v-spacer>
      <v-toolbar-side-icon @click.native.stop="drawer = !drawer" class="hidden-md-and-up"></v-toolbar-side-icon>
      <v-toolbar-items class="hidden-sm-and-down">
        <v-btn flat @click="$router.push('/')">Home</v-btn>
        <v-btn flat @click="$router.push('/about')">About</v-btn>
      </v-toolbar-items>

    </v-toolbar>
    <main>
      <v-container fluid class="pa-4">
        <router-view></router-view>
        <!--v-router-->
      </v-container>
    </main>
    <v-footer class="pa-3">
      <v-spacer></v-spacer>
      <div>© {{ new Date().getFullYear() }} | <a href="http://robertsoriano.com" target="_BLANK">robertsoriano.com</a></div>
    </v-footer>
  </v-app>
</template>


<script>
export default {
  name: 'app',
  data () {
    return {
      drawer: null,
      items: [
        { title: 'Home', icon: 'home' },
        { title: 'About', icon: 'question_answer' }
      ],
      mini: false,
      right: null
    }
  }
}
</script>

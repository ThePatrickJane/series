<template>
  <div class="nav">
    <v-navigation-drawer style="background: linear-gradient(to bottom right, #340a13 46%, #690f22 100%)" dark temporary v-model="sideNav" fixed width="250px">
      <v-layout column wrap align-center>
        <v-flex xs12 pb-4 pt-4>
          <router-link to="/" tag="span" style="cursor: pointer" mb-4>
            <span class="primary--text text-uppercase title">List</span>
            <span class="text-uppercase white--text font-weight-light title">Series</span>
          </router-link>
        </v-flex>
        <hr width="70%">
        <v-flex xs12 mt-3>
          <Login v-if="!userIsAuth"></Login>
        </v-flex>
        <v-flex xs12>
          <Signup v-if="!userIsAuth"></Signup>
        </v-flex>
        <v-flex xs12>
          <v-btn outline v-if="user" :to="{ name: 'profile', params: { id: user.id }}" flat>
            <span class="font-weight-light"> {{ user.firstName }} &nbsp;</span>
            <span> {{ user.lastName }} </span>
          </v-btn>
        </v-flex>
        <v-flex xs12>
          <v-btn flat color="white" v-if="userIsAuth" :to="{ name: 'tracker' }">
            <span>Tracker</span>
          </v-btn>
        </v-flex>
        <v-flex xs12>
          <v-btn flat v-if="userIsAuth" @click="logout">
            <span>Logout</span>
          </v-btn>
        </v-flex>
      </v-layout>
    </v-navigation-drawer>

    <v-toolbar app style="background: linear-gradient(to right, #340a13 46%, #690f22 100%)" dark clipped-left absolute>
      <v-toolbar-title>
        <router-link to="/" tag="span" style="cursor: pointer">
          <span class="primary--text text-uppercase">List</span>
          <span class="text-uppercase font-weight-light">Series</span>
        </router-link>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-toolbar-side-icon @click.stop="sideNav = !sideNav" class="hidden-sm-and-up"></v-toolbar-side-icon>
      <Login class="hidden-xs-only" v-if="!userIsAuth"></Login>
      <Signup class="hidden-xs-only" v-if="!userIsAuth"></Signup>
      <v-btn class="hidden-xs-only" flat color="white" v-if="userIsAuth" :to="{ name: 'tracker' }">
        <span>Tracker</span>
      </v-btn>
      <v-btn class="hidden-xs-only" v-if="user" :to="{ name: 'profile', params: { id: user.id }}" flat>
        <span class="font-weight-light"> {{ user.firstName }} &nbsp;</span>
        <span> {{ user.lastName }} </span>
      </v-btn>
      <v-btn class="hidden-xs-only" flat outline color="white" v-if="userIsAuth" @click="logout">
        <span>Logout</span>
      </v-btn>
    </v-toolbar>
  </div>
</template>

<script>
import Signup from './Signup'
import Login from './Login'
import { db } from '@/firebase'
import firebase from 'firebase'

export default {
  name: 'Navbar',
  components: {
    Signup,
    Login
  },
  data () {
    return {
      sideNav: false,
      user: null
    }
  },
  beforeMount () {
    firebase.auth().onAuthStateChanged(user => {
      if (user) {
        const userId = this.$store.getters.user.id
        db.collection('users').doc(userId).get().then(doc => this.user = doc.data())
      } else {
        this.user = null
      }
    })
  },
  computed: {
    userIsAuth () {
      return this.$store.getters.user !== null && this.$store.getters.user !== undefined
    }
  },
  methods: {
    logout () {
      this.$store.dispatch('logout')
      this.$router.push({ name: 'home' })
    }
  }
}
</script>

<style>

</style>

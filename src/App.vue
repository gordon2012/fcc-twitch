<template>
  <div id="app">
    <div class="container">
      <input v-model="searchTerm" v-on:keyup.enter="search" placeholder="Search...">
      <div class="users">
        <user v-for="(user, i) in loadedUsers()" :key="i" :user="user"/>
      </div>
      <div class="responses">
        <h3>Responses</h3>
        <div v-for="(user, i) in users" :key="i" class="response">
          <h4>{{ user.name }}</h4>
          <pre><code>
          {{ user }}
        </code></pre>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import User from './components/User';

const url = 'https://wind-bow.glitch.me/twitch-api';

export default {
  name: 'App',
  components: {
    User
  },
  methods: {
    loadedUsers: function() {
      return this.users.filter(user => {
        return !!user.user;
      });
    },
    search() {
      this.users.forEach(user => {
        fetch(`${url}/users/${user.name}`)
          .then(response => response.json())
          .then(data => {
            user.user = data;
            this.$forceUpdate();

            if (data.status !== 404) {
              fetch(`${url}/streams/${user.name}`)
                .then(response => response.json())
                .then(data => {
                  user.stream = data;
                  this.$forceUpdate();
                });
            }
          });
      });
    }
  },
  data() {
    return {
      searchTerm: '',
      users: [
        { name: 'ESL_SC2' },
        { name: 'OgamingSC2' },
        { name: 'cretetion' },
        { name: 'freecodecamp' },
        { name: 'storbeck' },
        { name: 'habathcx' },
        { name: 'RobotCaleb' },
        { name: 'noobs2ninjas' },
        { name: 'zijdijllalalal' }
      ],
      responses: {}
    };
  },
  mounted() {
    this.search();
    this.$watch('user', user => {
      console.log(user);
    });
  }
};
</script>

<style>
body {
  margin: 0;
}
h4 {
  margin: 0;
}
pre {
  white-space: pre-wrap;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  background: #eee;
}

.container {
  max-width: 1000px;
  margin: 0 auto;
}

.users {
  margin-bottom: 1em;
}

.user-top {
  border: 3px solid blue;
  display: flex;
}

.user-top-copy {
  padding: 0 1em;
  flex: 1;
  display: flex;
  align-items: center;
}

.user-bottom {
  border: 3px solid lime;
  padding: 1em;
}

.responses {
  border: 3px solid magenta;
  padding: 1em;
}

.response {
  border: 1px solid red;
  padding: 1em;
  margin-bottom: 0.5em;
}
</style>

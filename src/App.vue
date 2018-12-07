<template>
  <div id="app">
    <h1>Twitch.tv Monitor</h1>
    <div class="container">
      <input v-model="searchTerm" v-on:keyup.enter="addUser" placeholder="Search...">
      <div class="users">
        <user
          v-for="name in loadedNames()"
          :key="name"
          :name="name"
          :user="user[name]"
          :stream="stream[name]"
          @deleteUser="deleteUser(name)"
        />
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
    loadedNames: function() {
      return this.names.filter(name => {
        return !!this.user[name];
      });
    },

    fetchUser(name) {
      fetch(`${url}/users/${name}`)
        .then(response => response.json())
        .then(data => {
          this.user[name] = data;
          this.$forceUpdate();
          if (data.status !== 404) {
            fetch(`${url}/streams/${name}`)
              .then(response => response.json())
              .then(data => {
                this.stream[name] = data;
                this.$forceUpdate();
              });
          }
        });
    },

    search() {
      this.names.forEach(name => {
        fetch(`${url}/users/${name}`)
          .then(response => response.json())
          .then(data => {
            this.user[name] = data;
            this.$forceUpdate();
            if (data.status !== 404) {
              fetch(`${url}/streams/${name}`)
                .then(response => response.json())
                .then(data => {
                  this.stream[name] = data;
                  this.$forceUpdate();
                });
            }
          });
      });
    },
    deleteUser(name) {
      this.names = this.names.filter(n => n !== name);
      delete this.user[name];
      delete this.stream[name];
    },
    addUser() {
      const loc = this.names
        .map(name => name.toLowerCase())
        .indexOf(this.searchTerm.toLowerCase());

      if (loc === -1) {
        this.names = [this.searchTerm, ...this.names];
        this.fetchUser(this.searchTerm);
      } else {
        this.names = [
          this.names[loc],
          ...this.names.slice(0, loc),
          ...this.names.slice(loc + 1)
        ];
      }
      this.searchTerm = '';
    }
  },
  data() {
    return {
      searchTerm: '',
      names: [
        'ESL_SC2',
        'OgamingSC2',
        'cretetion',
        'freecodecamp',
        'storbeck',
        'habathcx',
        'RobotCaleb',
        'noobs2ninjas',
        'zijdijllalalal'
      ],
      user: {},
      stream: {},
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
  background: #eee;
  margin: 0;
}
h1 {
  text-align: center;
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
input {
  margin-bottom: 1em;
}
</style>

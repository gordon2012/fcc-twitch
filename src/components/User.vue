<template>
  <div class="user">
    <div class="heading">
      <img v-if="!user.error" :src="user.logo">
      <img v-else src="http://placeskull.com/100/100">
      <h2 v-if="user.error">{{name}}: User Not Found</h2>
      <h2 v-else>{{user.display_name}}</h2>
      <button @click="deleteUser">✖</button>
    </div>
    <div v-if="!user.error" class="body">
      <div class="bio">
        <p>{{user.bio}}</p>
      </div>
      <div v-if="stream && stream.stream !== null" class="stream">
        <div>
          <h4>Stream:</h4>
          <p>{{stream.stream.channel.status}}</p>
        </div>
        <img :src="stream.stream.preview.medium">
      </div>
      <h4 v-else>Not Streaming</h4>
    </div>
  </div>
</template>

<script>
export default {
  name: 'User',
  props: ['name', 'user', 'stream'],
  methods: {
    deleteUser() {
      this.$emit('deleteUser');
    }
  }
};
</script>

<style scoped>
.user {
  box-shadow: 1px 1px 1px rgba(0, 0, 0, 0.5);
  background: white;
  padding: 1em;

  margin-bottom: 1em;
}
.heading {
  display: flex;
  align-items: center;
}
.heading img {
  width: 100px;
  height: 100px;
  border-radius: 50%;
}
.heading h2 {
  padding-left: 1em;
  flex: 1;
}
.heading h4 {
  padding-right: 1em;
}
.stream {
  display: flex;
}
.stream > div {
  flex: 1;
}
button {
  font-size: 1.3em;
  border: none;
  background: none;
  cursor: pointer;
  align-self: flex-start;
}
</style>

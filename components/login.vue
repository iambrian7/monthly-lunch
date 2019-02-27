<template>
  <div>
    <div class="container">
      <h3>Login</h3>
      <input type="email" place-holder="email" v-model="email">
      <input type="password" place-holder="password" v-model="password">
      <button v-on:click="login" class="">Login</button>
  </div>
  </div>
</template>

<script>
// import firebase from 'firebase';
// export default {
   module.exports = {
  name: 'login',
  // props: ['firebase'] ,
  data: function() {
    return {
      email: '',
      password: ''
    };
  },
  methods: {
    login: function(e) {
      firebase
        .auth()
        .signInWithEmailAndPassword(this.email, this.password)
        .then(
          user => {
        //    alert(`You are logged in as ${user.email}`);
         //   debugger
            this.$router.push({ path: '/' })

          //  this.$router.go({ path: "/home"});
           //  this.$router.go({ path: this.$router.path });

          },
          err => {
            alert(err.message);
          }
        );
      e.preventDefault();
    }
  }
};
</script>
<style>
.container{
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.container > * {
  margin: 10px;
  padding: 20px;
}
</style>

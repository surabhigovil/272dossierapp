<template>
  <div id="app">
    <amplify-authenticator>
      <div>
        <h1>Hey, {{user.username}}!</h1>
        <label>File
          <input type="file" id="file" ref="file" v-on:change="onFileChange()"/>
        </label>
        <amplify-sign-out></amplify-sign-out>
      </div>
    </amplify-authenticator>
  </div>
</template>
<script>
import { AuthState, onAuthUIStateChange } from '@aws-amplify/ui-components'
import { Auth, Storage} from 'aws-amplify';

Auth.currentAuthenticatedUser().then(console.log('User authenticated'))

export default {
  name: 'app',
  data() {
    return {
      user: { },
    }
  },
  methods: {
    onFileChange(){
      this.file = this.$refs.file.files[0];
      if (this.file)
        console.log("file is present");
      if (this.file) {
        Storage.put(`userimages/${this.$refs.file.files[0].name}`, this.$refs.file.files[0],{ contentType: this.$refs.file.files[0].type })
        .then (result => console.log(result)) // {key: "test.txt"}
        .catch(err => console.log(err));

        Storage.get(`userimages/${this.$refs.file.files[0].name}`, {download: true}) // for listing ALL files without prefix, pass '' instead
        .then(result => console.log(result))
        .catch(err => console.log(err));
      }
    },
  },  
  created() {
    // authentication state managament
    onAuthUIStateChange((state, user) => {
      // set current user and load data after login
      if (state === AuthState.SignedIn) {
        this.user = user;
      }
    })
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

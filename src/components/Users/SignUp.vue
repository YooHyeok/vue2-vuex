<template>
  <div>
    <h1>SignUp</h1>
    <v-text-field
      label="아이디"
      v-model="userId"
    ></v-text-field>
    <v-text-field
      label="비밀번호"
      type="password"
      v-model="password"
    ></v-text-field>
    <v-text-field
      label="이름"
      v-model="name"
    ></v-text-field>
    <v-text-field
      label="주소"
      v-model="address"
    ></v-text-field>
    <v-text-field
      label="이미지"
      v-model="src"
    ></v-text-field>
    <v-btn @click="signUp">회원가입</v-btn>
  </div>
</template>

<script>
import { EventBus } from '@/main.js'
// import { mapMutations } from 'vuex'
import { mapActions } from 'vuex'
  export default {
    data() {
      return {
        userId: null,
        password: null,
        name: null,
        address: null,
        src: null
      }
    },
    methods: {
      // ...mapMutations(['addUsers']),
      // ...mapMutations({usersAdd:'addUsers'}),
      // ...mapActions(['addUsers']),
      // ...mapActions({usersAdd:'addUsers'}),
      signUp() {
        let userObj = {
          userId: this.userId,
          password: this.password,
          name: this.name,
          address: this.address,
          src: this.src
        }
        // EventBus.$emit('signUp', userObj)
        // this.addUsers(userObj)

        /* mutations */
        // this.usersAdd(userObj) //mapMutation
        // this.$store.commit('addUsers', userObj)

        /* actions */
        // this.addUser(userObj) //mapActions
        // this.usersAdd(userObj) //mapActions
        this.store.dispatch('addUsers',userObj)
        this.clearForm()
      },
      clearForm() {
        this.userId = null,
        this.password = null,
        this.name = null,
        this.address = null,
        this.src = null
      }
    }
  }
</script>
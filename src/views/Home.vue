<template>
  <v-container>
    <h1>Home</h1>
    <h4>모든 회원 수 : {{/*$store.state.allUsers.length*/ $store.getters.allUsersCount}}</h4>
  </v-container>
</template>

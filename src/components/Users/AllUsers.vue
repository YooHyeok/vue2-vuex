<template>
  <div>
    <!-- <h1>All Users: {{$store.state.allUsers.length }}</h1> -->
    <!-- <h1>All Users: {{$store.getters.allUsersCount }}</h1> -->
    <h1>All Users: {{allUsersCount }}</h1>
    <!-- <h3>Seoul Users: {{$store.getters.countOfSeoul}} ({{ $store.getters.percentOfSeoul }})%</h3> -->
    <h3>Seoul Users: {{countOfSeoul}} ({{ percentOfSeoul }})%</h3>
    <v-list two-line>
      <v-list-tile 
        v-for=" (user, index) in /*$store.state.allUsers*/ /*allUsers*/ totalUsers "
        :key="index"
        avatar
      >
        <v-list-tile-avatar color="grey lighten-3">
          <img :src="user.src">
        </v-list-tile-avatar>

        <v-list-tile-content>
          <v-list-tile-title v-html=" user.name "></v-list-tile-title>
          <v-list-tile-sub-title>id:#{{index}} / {{user.address}} 거주</v-list-tile-sub-title>
        </v-list-tile-content>
      </v-list-tile>
    </v-list>

  </div>
</template>

<script>
import { EventBus } from '@/main.js'
import { mapState, mapGetters } from 'vuex'
  export default {
    data() {
      return {
        allUsers:[
          {userId: 'hoza123', password: '123', name: 'Hoza', address: 'Seoul', src:'https://goo.gl/oqLfJR'},
          {userId: 'max123', password: '456', name: 'Max', address: 'Berlin', src:'https://goo.gl/Ksk9B9'},
          {userId: 'lego123', password: '789', name: 'Lego', address: 'Busan', src:'https://goo.gl/x7SpCD'}
        ]
      }
    },
    computed: {
      // ...mapGetters(['allUsersCount', 'countOfSeoul', 'percentOfSeoul'])
      ...mapGetters({
        allUsersCount: 'allUsersCount', 
        countOfSeoul: 'countOfSeoul', 
        percentOfSeoul: 'percentOfSeoul'
      }),
      // ...mapState(['allUsers']) // data와 중복되면 콘솔에 warn으로 출력된다.
      ...mapState({        
        totalUsers: 'allUsers',
    })
    },
    mounted() {
      EventBus.$on('signUp', users => {
        // this.allUsers.push(users)
        this.$store.state.allUsers.push(users)
      })
    }
  }
</script>

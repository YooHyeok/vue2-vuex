# Vuex 란
vue.js 애플리케이션의 상태관리 패턴 + 라이브러리 이다.  
애플리케이션의 모든 컴포넌트에 대한 중앙 집중식 저장소 역할을 하며 예측 가능한 방식으로 상태를 변경할 수 있다.  
디아블로 게임을 예로 들자면 특정 마을들에가면 공통적으로 어떤 상자가 있다.  
해당 상자에는 아이템을 넣을 수 있으며, 한번 넣은 아이템은 어떠한 마을에서도 넣었을 당시 상태 그대로 공유할 수 있게 된다.  
여기서 마을의 이동은 라우터를 통한 컴포넌트 전환이며, 다른 컴포넌트에서도 동일한 상태나 함수를 저장소로부터 꺼내어 쓸 수가 있다.  
실제로 Vuex가 어떻게 동작하는지 알아보자.  
```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {},
  getters: {},
  mutations: {},
  actions: {}
})

```

- ### `state`  
  vue 인스턴스에서의 data와 같은 역할을 해준다.  
  ```js
  import Vue from 'vue'
  import Vuex from 'vuex'

  Vue.use(Vuex)

  export default new Vuex.Store({
    state: {
      dataList: ["데이터1","데이터2","데이터3"]
    },
  })

  ```
  .vue 파일에서 $store.state.{변수명}을 통해 접근 가능하다.
  ```vue
  <script>
  export default {
    computed: {
      allUsers:() {
        return this.$store.state.dataList;
      }
    },
    methods: {
      signUp() {
        this.$store.state.dataList.push(user);
      }
    }
  }
  </script>
  ```
- ### `getters`  
  중앙 통제 관리 저장소 구조에서 발생할 수 있는 흔한 문제점 중 하나로 컴포넌트에서 vuex의 데이터에 접근할 때 중복된 코드를 반복 호출하게 되며, 이를 위해서 Getters 라는 것이 존재한다.  
  ```vue
  <template>
    <div>
      <h1>{{$store.state.dataList.length}}</h1>
    </div>
  </template
  ```
  위 코드가 특정 컴포넌트들 에서 반복되서 사용된다.  
  이렇게 반복된 코드를 줄여줄 수 있는, 즉 간단한 계산식을 캐싱해 주는 것  
  바로 `computed`라는 속성이 있다.
  이 `computed`과 같은 역할을 해주는 것이 바로 `Getters` 이다.
  ```js
  import Vue from 'vue'
  import Vuex from 'vuex'

  Vue.use(Vuex)

  export default new Vuex.Store({
    state: {
      dataList: ["데이터1","데이터2","데이터3"]
    },
    getters: {
      // dataCnt: function(state) {
      dataCnt: function(state) {
        return state.dataList.length
      }
    },
  })
  ```
  .vue 파일에서 $store.getters.{변수명}을 통해 접근 가능하다.
  ```vue
  <template>
    <div>
      <h1>All Users{{/*$store.state.dataList.length*/ $store.getters.dataCnt}}</h1>
    </div>
  </template>
  ```


- mutation: 
- action: 

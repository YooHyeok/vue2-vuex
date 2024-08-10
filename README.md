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

- ## `state`  
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
- ## `getters`  
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
      dataCnt(state) {
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
  #### getters 내 getters 접근
  - store.js
    ```js
    import Vue from 'vue'
    import Vuex from 'vuex'

    Vue.use(Vuex)

    export default new Vuex.Store({
      state: {
        dataList: ["데이터1","데이터2","데이터3"]
      },
      getters: {
        dataCount: state => {
          return state.dataList.filter(data => data === '데이터3').length;
        },
        dataPercent: (state, getters)=> {
          return Math.round(getters.dataCount / 3 * 100);
        },
      },
    })
    ```
    위와 같이 두번째 매개변수로 getters 속성에 접근하여 함수를 호출할수도 있다.
  #### mapGetters
  getters의 map을 제공해준다는 의미로, store.js 파일의 getters에 선언되어있는 함수들을 쉽게 접근해서 사용할 수 있도록 도와준다.  
  컴포넌트 파일의 script태그 내에 vuex로부터 mapGetters를 import하고 `coumputed`훅을 통해 사용한다.  
  문법이 좀 특이한데, 전개식을 통해 import한 mapGetters를 전개하여 배열 형태로 getters에 선언한 메소드중 사용할 메소드명을 String 문자열로 담아준다.  
  - .vue 파일 예제
  ```vue
  <template>
    <div>
      <h3>data: {{dataCount}} ({{ dataPercent }})%</h3>
    </div>
  </template>

  <script>
  import { mapGetters } from 'vuex'
    export default {
      computed: {
        ...mapGetters(['dataCount', 'dataPercent'])
      }
    }
  </script>
  ```
  전개식 문법을 사용한 mapGetters 함수는 객체형태인 것으로 예측할 수 있고, mapGetters의 배열 인자로 담은 String 메소드명을 getters로 부터 매핑하여 해당 객체에 각각 매핑하는 것으로 예측할 수 있다.

  - `...mapGetters(['dataCount', 'dataPercent'])` 결과 예측 코드
  ```vue
  <template>
    <div>
      <h3>data: {{dataCount}} ({{ dataPercent }})%</h3>
    </div>
  </template>

  <script>
    export default {
      computed: {
        dataCount: function(){return this.$store.getters.allUsersCount}
        dataPercent: function(){return this.$store.getters.dataPercent}
      }
    }
  </script>
  ```
  #### 객체 형태로도 사용 가능
  ```vue
  <template>
    <div>
      <h3>data: {{dataCount}} ({{ dataPercent }})%</h3>
    </div>
  </template>
  <script>
  import { mapGetters } from 'vuex'
    export default {
      computed: {
        ...mapGetters({
          allUsersCount: 'allUsersCount', 
          countOfSeoul: 'countOfSeoul', 
          percentOfSeoul: 'percentOfSeoul'
      })
      }
    }
  </script>
  ```
성에 접근하여 함수를 호출할수도 있다.

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

  #### mapState
  state의 map()을 제공해준다는 의미로, store.js 파일의 state에 선언되어있는 변수들을 쉽게 접근해서 사용할 수 있도록 도와준다.  
  컴포넌트 파일의 script태그 내에 vuex로부터 mapState를 import하고 `coumputed`훅을 통해 사용한다.  
  문법이 좀 특이한데, 전개식을 통해 import한 mapmapState를 전개하여 배열 형태로 state에 선언한 메소드중 사용할 메소드명을 String 문자열로 담아준다.  
  - .vue 파일 예제
    ```vue
    <template>
      <div>
        <h3>dataList: {{dataList}} </h3>
      </div>
    </template>

    <script>
    import { mapState } from 'vuex'
      export default {
        computed: {
          ...mapState(['dataList'])
        }
      }
    </script>
    ```
  #### 객체 형태로도 사용 가능
  만약 컴포넌트내 data 변수와 변수명이 일치한다면 콘솔에 아래와 같은 에러가 출력된다.  
  `[Vue warn]: The computed property "dataList" is already defined in data.`  
  아래 코드 예시와 같이 객체 형태로 변수명에 대한 property를 바꿀수 있다.
  ```vue
  <template>
    <div>
      <h3>dataList: {{datas}} </h3>
    </div>
  </template>

  <script>
  import { mapState } from 'vuex'
    export default {
      computed: {
        ...mapState({datas: 'dataList'})
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
  만약 컴포넌트내 methods 훅에 선언한 함수명과 일치한다면 콘솔에 아래와 같은 에러가 출력된다.  
  `컴포넌트명.vue:28 [Vue warn]: The computed property "dataCount" is already defined as a method.`  
  아래 코드 예시와 같이 객체 형태로 함수명에 대한 property를 바꿀수 있다.
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

- ## `mutation`  
  한글 공식문서에서는 변이 라고 설명되며, 말 그대로 state값을 변화시키는 역할을 한다.  
  컴포넌트 내 어떤 함수를 통해 state에 있는 dataList 값을 직접 변화시키는 것이 아니라, mutation을 사용하여 동작하도록 한다.  
  컴포넌트 내에서도 state의 직접적인 변화를 유도할 수 있는데 mutation이 왜 필요한것일까?  
  여러 컴포넌트에서 하나의 동일한 state를 공유하고 있고, 각각의 컴포넌트에서 state를 변경하는 기능이 존재한다면?  
  해당 기능이 같은 기능을 하는 함수라고 하면 똑같은 기능을 하는 함수인데도, 각 컴포넌트에 동일하게 선언해야 하는 불편함이 있기 때문이다.  
  같은 기능을 하는 함수를 mutation내에 만들어 놓고 해당 mutation을 각각의 컴포넌트에서 실행시키는 것이다.  
  이렇게 실행시키는 방식을 `commit` 이라고 부른다.  
  각각의 컴포넌트 내에서 함수들로 state에 접근하는 것이 아니라, 각각의 컴포넌트내에서 mutation내에 저장되어 있는 함수를 `commit` 해서 state값을 변화시킨다.  
  이렇게 되면 같은 기능을 하는 함수들을 각각의 컴포넌트에 선언하지 않고 mutation을 불러오기만 하면 되므로 훨씬 더 코드를 간단하게 작성할 수 있게 된다.  
  일반적으로 commit의 뜻은 (공개적으로) 의사[결정]를 밝히다 라는 의미로, state를 변경 하겠다는 의사를 vuex에게 밝히고, vuex가 그 결정에 맞게 state를 변경해 주는것이다.  
  **mutation(변경의사) → state(변경반영) → getters(반영전달) ← COMPONNENTS**
  - store.js
    ```js
    import Vue from 'vue'
    import Vuex from 'vuex'

    Vue.use(Vuex)

    export default new Vuex.Store({
      state: {
        dataList: ["데이터1","데이터2","데이터3"]
      },
      mutations: {
        dataPush: (state, payload) => {
          state.dataList.push(payload)
        }
      }
    })
    ```
    매개변수는 첫번째 인자로 state와 두번째 인자로 payload를 받는다.  
    payload는 mutations에 선언된 함수를 호출할 때 넘겨주는 인자이다.  
    ### mapMutations
    mutation은 실행하는 함수이기 때문에 mapMutations는 다른 mapStates, mapGetters에서 computed에 선언했던 방식을 따르지 않고 methods에서 전개식을 통해 호출한다.
    ```vue
    <script>
    import { mapMutations } from 'vuex'
    export default {
      methods: {
        ...mapMutations('dataPush'),
        pushData() {
          this.dataPush('데이터4'); //mutation 호출 payload: '데이터4'
        }

      }
    }
    </script>
    ```
    #### 객체 형태로도 사용 가능
    만약 컴포넌트내 methods 훅에 선언한 함수명과 일치한다면 콘솔에 아래와 같은 에러가 출력된다.  
    `컴포넌트명.vue:28 [Vue warn]: The computed property "dataCount" is already defined as a method.`  
    아래 코드 예시와 같이 객체 형태로 함수명에 대한 property를 바꿀수 있다.
    ```vue
    <script>
    import { mapMutations } from 'vuex'
      export default {
        methods: {
            ...mapMutations({
            pushData: 'dataPush', 
          })
        },
        dataPush() {
          this.pushData({'데이터4'});
        }
      }
    </script>
    ```
    ### commit을 통한 mutation 호출
      ```vue
      <script>
      export default {
        methods: {
          pushData() {
            this.$store.commit('dataPush','데이터4'); //mutation 호출 payload: '데이터4'
          }
        }
      }
      </script>
      ```


- action: 

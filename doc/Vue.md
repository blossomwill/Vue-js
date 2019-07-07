# Vue는 무엇인가?

##### View

화면에 나타나는 입력, 버튼, 글과 같은 요소인 HTML이 있고 DOM이란 javascript로 조작하게 된다.

##### ViewModel

DOM Event를 DOM Listener가 잡아서 javascript(model)의 값을 바꿔주게 된다

javascript에서 바뀐 데이터를 잡아서(Data Binding) 화면에 나타내준다



## 7월 2일 학습내용

### 기존의 웹개발

기존의 웹개발은 HTML, Javascript, CSS를 가지고 개발을 한다

HTML은 화면에 나타나는 태그, DOM을 작성하고 Javascript는 태그, DOM을 조작한다.

* HTML에 태그를 정의하고 javascipt로 태그의 정보를 받아와서 변수에 할당해서 조작한다.

```html
<body>
    <div id="app"></div>

    <script>
        //$('#app');
        let div = document.querySelector('#app');
        console.log(div);
        let str = 'hello world';
        div.innerHTML = str;

        str = 'hello world!!!'
        div.innerHTML = str;
    </script>
</body>
```

* `div#app + tap`: div 태그 자동완성
* `$('#app'); == document.querySelector('#app');` 두 문법은 동일하다
* DOM인 div태그가 화면에 나타나고 javascript는 div태그를 조작해서 내용을 바꾼다.



### reactivity 구현

* console창으로 값에 접근하면 get 함수가 실행하고, 해당 객체의 속성의 값을 바꾸면 set함수가 실행한다.
* 이러한 기능으로 속성변경으로 화면의 값을 바꿀수 있다.

* ````html
  <body>
      <div id="app"></div>
  
      <script>
          let div = document.querySelector('#app');
          div.innerHTML = 'hello world';
          var viewModel = {};
          // (대상 객체, 객체의 속성, {
          //  // 정의할 내용
          //})
          Object.defineProperty(viewModel, 'str', {
              // 속성에 접근했을 때의 동작을 정의
              get: function() {
                  console.log('접근');
              },
              // 속성에 값을 할당했을 때의 동작을 정의
              set: function(newValue) {
                  console.log('할당');
                  div.innerHTML = newValue;
              }
          })
      </script>
  </body>
  ````

* Vue의 특징: 데이터의 변화를 라이브러리에서 감지해서 알아서 화면을 변경해주는 것

### Reactivity 코드 라이브러리화 하기

* 위에서 작성한 Object.defineProperty를 init 함수안에 작성하고 init()실행을한다.
* 위의 init함수 정의와 실행을 javascript 즉시 실행 함수에 넣어주어 외부파일로부터 접근을 제한하는 유효범위를 줘서 유지보수를 쉽게한다 -> opensource에서 주로 쓰는 방법

```html
<script>
        let div = document.querySelector('#app');
        div.innerHTML = 'hello world';
        let viewModel = {};
        // (대상 객체, 객체의 속성, {
        //  // 정의할 내용
        // })
        // 즉시 실행 함수
        (function() {
            function init() {
                Object.defineProperty(viewModel, 'str', {
                    // 속성에 접근했을 때의 동작을 정의
                    get: function() {
                        console.log('접근');
                    },
                    // 속성에 값을 할당했을 때의 동작을 정의
                    set: function(newValue) {
                        console.log('할당');
                        render(newValue);
                    }
                });
            }
            function render(value) {
                div.innerHTML = value;
            }
            init();
        })();
        
    </script>
```



### 인스턴스 소개

우리는 new Vue()로 Vue에서 미리 만들어놓은 생성자함수를 이용해서 인스턴스를 생성한다. 우리는 인스턴스를 정의 하면서 Vue에서 미리 만든 속성, API를 사용, 재할당하게 된다.

Vue를 사용하려면 Vue를 만들면서 태그를 할당하는 el이 필요하다. `el`은 body에서 해당 태그를 찾아서  태그를 조작하도록 Vue의 기능과 속성을 유효화시킨다. 해당 태그의 값을 조작하려면 data의 message의 값을 변경하면 된다.

```html
<body>
    <div id="app">
        <!-- ... -->
    </div>
    
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        let vm = new Vue({
            el: '#app', // body Tag 안에서 #app을 찾아서 인스턴스를 붙이겠다. Vue의 기능과 속성을 유효화한다
            data: {
                message: 'h1'
            }
        })
    </script>
</body>
```



### 뷰 컴포넌트

>  화면의 영역을 영역별로 구분해서 컴포넌트기반으로 코드를 관리
>
> 재사용성이 올라가고 빠르게 화면 제작 가능
>
> 영역 구분을 하면 컴포넌트끼리의 관계가 발생



### 컴포넌트 등록

인스턴스를 생성하면 Root 컴포넌트가 된다.

##### 전역컴포넌트 등록

* `Vue.Component('컴포넌트 이름', 컴포넌트 내용);`
* 컴포넌트 태그는 컴포넌트 이름과 같고, 작성한 컴포넌트 태그는 인스턴스영역 안에서 작성되어야 한다.
* Root 밑에 component가 등록된다.

```html
<body>
    <div id="app">
            <app-header></app-header>
    </div>
       
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        // 전역 컴포넌트
        // Vue.component('컴포넌트 이름', 컴포넌트 내용);
        Vue.component('app-header', {
            template: '<h1>Header</h1>'
        });
      
        new Vue({
            el: '#app',
        });
    </script>
</body>
```

##### 지역 컴포넌트 등록

```html
<body>
    <div id="app">
            <app-footer></app-footer>
    </div>
    
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        new Vue({
            el: '#app',
            // 지역 컴포넌트 등록 방식
            components: {
                // '컴포넌트 이름': 컴포넌트 내용,
                'app-footer': {
                    template: '<footer>footer</footer>'
                }
            }
        });
    </script>
</body>
```



### 전역 컴포넌트와 지역 컴포넌트의 차이점

* 지역 컴포넌트는 하단에 어떤 컴포넌트가 등록되었는지 알 수 있다.
* 전역 컴포넌트는 실제 서비스에서 플러그인이나 라이브러리 형태로 전역으로 사용해야 하는 경우만 쓴다.
* 일반적으로 지역 컴포넌트를 사용한다.



### 컴포넌트와 인스턴스의 관계

* 인스턴스를 생성하면 Root 컴포넌트가 생성된다
* 전역 컴포넌트는 모든 인스턴스에 등록된다.
  * 모든 인스턴스 영역에서 컴포넌트 태그를 사용할 수 있다.
* 지역 컴포넌트는 다른 인스턴스에서 사용할수 없다.
* 서비스는 인스턴스를 하나 생성하고 그 안에 컴포넌트들을 만들어서 구성한다.

```html
<body>
    <div id="app">
            <app-header></app-header>
            <app-footer></app-footer>
    </div>
        
    <div id="app2">
            <app-header></app-header>
            <app-footer></app-footer>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        // 전역 컴포넌트
        // Vue.component('컴포넌트 이름', 컴포넌트 내용);
        Vue.component('app-header', {
            template: '<h1>Header</h1>'
        });
    

        new Vue({
            el: '#app',
            // 지역 컴포넌트 등록 방식
            components: {
                // '컴포넌트 이름': 컴포넌트 내용,
                'app-footer': {
                    template: '<footer>footer</footer>'
                }
            }
        });

        new Vue({
            el: '#app2',

        })

    </script>
</body>
```



### 컴포넌트 통신

컴포넌트는 각각 고유한 데이터 유효범위를 갖는다. 

* 상위에서 하위로는 데이터를 내려줌, `프롭스 속성`
* 하위에서 상위로는 이벤트를 올려줌, `이벤트 발생`

* 상 하위로만 통신을 주고 받아야 버그가 발생할 가능성이 줄어든다.



### props

* 상위에서 하위로 내려줄 때 하위 컴포넌트에서 데이터를 props 속성으로 받는다.
* 하위 컴포넌트 태그에서 하위의 props와 상위의 data를 `v-bind`로 묶어주면 상위의 데이터에 따라 하위의 props의 값이 바뀐다.
* `<app-header v-bind:프롭스 속성 이름="상위 컴포넌트의 데이터 이름">`

```html
<body>
    <div id="app">
        <!-- <app-header v-bind:프롭스 속성 이름="상위 컴포넌트의 데이터 이름"></app-header> -->
        <app-header v-bind:propsdata="message"></app-header>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        let appHeader = {
                template: '<h1>{{ propsdata }}</h1>',
                props: ['propsdata']

            }
        
        new Vue({
            el: '#app',
            components: {
                'app-header': appHeader
            },
            data: {
                message: 'h1'
            }
        })
    </script>
</body>
```



### emit

하위 컴포넌트에서 발생한 이벤트(이름)로 상위의 메서드를 실행할 수 있다.

* `v-on:click`: click시 함수를 실행할 수 있는 Vue의 속성

1. 하위에서 emit 생성할 method 작성
2. emit을 발견했을 때 실행할 상위의 메서드 작성
3. 하위에서 emit 발생
4. 하위 컴포넌트 태그에서 `v-on:발생emit=상위 메서드`로 연결한 메서드가 실행

* 뷰 개발자 도구의 event에서 어떤 이벤트가 발생했는지 알 수 있다.
* 인스턴스 영역에서 `{{ 데이터이름 }}`하면 화면에 나타내서 확인 가능

```html
<body>
    <div id="app">
        <!-- <app-header v-on:하위 컴포넌트에서 발생한 이름="상위 컴포넌트의 메서드 이름"></app-header> -->
        <app-header v-on:pass="logText"></app-header>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var appHeader = {
            template: '<button v-on:click="passEvent">click me</button>',
            methods: {
                passEvent: function() {
                this.$emit('pass');
                }
            }
        }
   
        new Vue({
            el: '#app',
            components: {
                'app-header': appHeader,
            },
            methods: {
                logText: function() {
                    console.log('hi');
                },
            },
         });
    </script>
</body>
```



### 같은 컴포넌트 레벨 간의 통신 방법

같은 부모 컴포넌트의  method에 emit으로 값 넘기고, 부모 컴포넌트는 자식 컴포넌트의 props에 data로 값을 넘긴다

* 부모: Root
* 자식: app-header, app-content

* app-content의 버튼을 클릭하면 emit을 발생시키는 passNum 메소드를 실행한다.
* 발생된 emit은 값을 부모의 메소드은 deliverNum에 넘긴다. 메소드에 인자 받는 구문이 정의되있더라도 뷰 디렉티브에서는 인자를 표시해줄 필요없이 메소드명을 적으면된다.
* Root 메소드에서는 넘겨받은 값을 Root의 데이터인 num에 할당한다.
* app-header는 props를 갖고 있고 Root의 num을 뷰디렉티브를 통해 props 변수에 저장한다
  * props 변수는 대문자가 올수 없다. -> 인식 못함
* app-header는 props에 동일 레벨 컴포넌트 app-content로 넘겨받은 값을 갖게된다.
* `v-bind:프롭스속성이름="상위 컴포넌트의 데이터 이름"`
* `v-on:emit이름="상위 컴포넌트의 메소드 이름 "`

```vue
<div id="app">
        <app-header v-bind:props-num="num"></app-header>
        <app-content v-on:pass="deliverNum"></app-content>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        let appHeader = {
            template: '<div>header</div>',
            props: ['props-num']
        }
        let appContent = {
            template: '<div>Content<button v-on:click="passNum">pass</button></div>',
            methods: {
                passNum: function() {
                    this.$emit('pass', 10)
                }
            }
        }
        new Vue({
            el: '#app',
            components: {
                'app-header': appHeader,
                'app-content': appContent,
            },
            data: {
                num: 0
            },
            methods: {
                deliverNum: function(value){
                    this.num = value;
                }
            }
        })
    </script>
```



### 뷰 라우터 소개와 설치

뷰 라우터는 뷰 라이브러리를 이용하여 SPA를 구현할때 사용하는 (공식)라이브러리로, 페이지를 이동할 때 사용한다.

* `사용`: 뷰 라우터 인스턴스를 생성하고 뷰 인스턴스에 등록한다.

* `routes`:  페이지의 라우팅 정보(path, name, component)가 있는 라우터객체 지정 속성
  * 페이지마다 component는 1개만 등록

* `mode`: URL의 해쉬 값 제거 속성
  * `mode: 'history'`

##### router-view

* 페이지 url이 변경됐을 때 해당 url의 컴포넌트 내용을(컴포넌트의 template이겠지?) 나타내는 태그 영역

##### router-link

* 라우터에서 페이지 이동을 하기위한 링크태그
  * `<router-link to="이동할 url"></router-link>` 

**라우터 추가로 알아보기**: [라우터 네비게이션](<https://joshua1988.github.io/web-development/vuejs/vue-router-navigation-guards/>)

> router.html

```html
<body>
    <div id="app">
        <div>
            <router-link to="/login" style="text-decoration:none;color:aqua;">Login</router-link>
            <router-link to="/main">Main</router-link>
        </div>
        <router-view></router-view>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>
    <script>
        let LoginComponent = {
            template: '<div>login</div>'
        }
        
        let MainComponent = {
            template: '<div>main</div>'
        }

        let router = new VueRouter({
            // 페이지의 라우팅 정보
            routes : [
                // 로그인 페이지 정보
                {
                    // 페이지의 url
                    path: '/login',
                    // 해당 url에서 표시된 컴포넌트
                    component: LoginComponent,                
                },
                // 메인 페이지 정보
                {
                    path: '/main',
                    component: MainComponent,
                }
            ]
        });

        new Vue({
            el: '#app',
            router: router,
        });
    </script>

</body>
```

### 자바스크립트의 비동기 처리와 콜백

> 자바스크립트의 비동기 처리란 특정 코드의 연산이 끝날 때까지 코드의 실행을 멈추지 않고 다음 코드를 먼저 실행하는 자바스크립트의 특성을 의미한다.

```
자바스크립트의 비동기 처리 패턴
1. callback
2. promise
3. promise + generator
4. async & await
```

* `callback`: 함수를 호출한 결과가 나온 시점에서만 동작을 수행하도록 한다.

함수를 호출하면 콜백함수가 완료 될 때까지 해당 함수의 데이터를 처리하는 구문을 실행하지 않고 다음 코드를 실행하다가 콜백함수가 완료됐다고 부르면 함수의 동작을 수행하도록 해준다.

### Promise

* 데이터를 받아오기 전에 화면에 데이터를 표시하는 오류를 해결하기 위한 방법

  

### axios

Vue에서 권고하는 Pomise 기반의 HTTP 통신 라이브러리

`구조`: config, data,  headers, request

#### 오픈소스 사용 Tip

* 사용 전 Star, commits, contributors 수 확인
* 수정 날짜 확인

* use

axios.get 요청을 보내서 response를 받는다. Vue의 data에 reponse데이터를 할당해서 사용한다.

```javascript
new Vue({
            el: '#app',
            data: {
                users: []
            },
            methods: {
                getData: function() {
                    axios.get('https://jsonplaceholder.typicode.com/users/')
                    .then(function(response) {
                        console.log(response.data);
                        this.users = response.data;
                      })
                      .catch(function(error) {
                        console.log(error);
                      });
                }
            }
        })
```

axios 호출 전의 this는 Vue 인스턴스를 가리키지만 axios안의 this는 Window 전역을 가리킨다.



### 웹 서비스에서의 클라이언트와 서버와의 HTTP 통신구조

* `브라우저(Client)` --- HTTP Request(요청) ---> `서버(특정 백엔드 로직)` --- 값 꺼내기---> `DB`

* `서버` --- HTTP Response(응답) ---> `브라우저(Client)`

### 개발자 도구  network 패널 보기

XHR이라는 비동기 통신만 filtering

`Name의 Header`: 특정 요청, 응답에 대한 부가적인 정보. General에서 요청 URL, Method, Status Code를 볼 수 있다.

### 뷰의 템플릿 문법

뷰로 화면을 조작하는 방법. 데이터 바인딩과 디렉티브로 나뉜다.

`데이터 바인딩`: 뷰 인스턴스에서 정의한 속성을 화면에 표시하는 방법 {{ }}

`디렉티브`: html에서 `v-`가 붙는 속성. 뷰로 화면의 요소를 쉽게 조작하는 문법, 인스턴스와 태그를 연결한다.

* 

```html
<div id="app">
        <p>{{ num }}</p>
        <p>{{ doubleNum }}</p>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        new Vue({
            el: '#app',
            data: {
                num: 10,
            },
            computed: {
                doubleNum: function() {
                    return this.num * 2;
                }
            }
        })
    </script>
```

* 인스턴스의 속성인 num은 화면에서 {{ num }}으로 나타나게한다.
* 데이터의 값에 따라 바뀌는 데이터는 data에 정의하지 않고 `computed` 속성에 정의한다.

### 뷰 디렉티브와 v-bind

* `v-` 구문이 있으면 Vue의 data 속성과 computed 속성에서 변수, 객체를 찾는다.

```html
<div id="app">
        hello<p v-if='show'>Vue.js</p>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        new Vue({
            el: '#app',
            data: {
                show: true
            }
        })
    </script>
```



* `v-bind`: html의 속성과 Vue인스턴스의 속성을 연결
  * html의 id, class 등과 Vue의 data속성, computed속성을 연결

```html
<div id="app">
        <p v-bind:id="uuid" v-bind:class="name">{{ num }}</p>
    <p>{{ doubleNum }}</p>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        new Vue({
            el: '#app',
            data: {
                num: 10,
                uuid: 'abc1234',
                name: 'text-blue',
            },
            computed: {
                doubleNum: function() {
                    return this.num * 2;
                }
            }
        })
    </script>
```

### v-if, v-show

* v-if는 if - else 문과 동일한데 구문에 Vue의 속성을 사용한다. 

  * 조건 만족 시 태그를 실행하고 아니면 태그가 Dom에서 제거된다.

* v-show는 조건만족시 태그를 실행하고 아니면 `display:none`이 되어 육안상 사라지지만(공간도 잡지않지만)

  DOM에 정보가 남아있다.

  ```html
  <div id="app">
          <div v-if="loading">
              Loding...
          </div>
          <div v-else>
              test user has been logged in
          </div>
          <div v-show="loading">
              Loding...
          </div>
      </div>
  
      <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
      <script>
          new Vue({
              el: '#app',
              data: {
                  loading: true
              },
          })
      </script>
  ```

  

### 공식문서를 보고 해결하는 방법

##### input

```html
<!-- TOD0: 인풋 박스를 만들고 입력된 값을 p태그에 출력해부세요. -->
        <input type="text" v-model="message">
        <p>{{ message }}</p>
		new Vue({
            el: '#app',
            data: {
                num: 10,
                uuid: 'abc1234',
                name: 'text-blue',
                loading: true,
                message: '',
            },
		})
```



### methods 속성과 v-on 디렉티브를 이용한 키보드, 마우스 이벤트 처리

`v-on:` : 이벤트 발생과 Vue의 인스턴스를 연결

`이벤트 모디파이어`: 이벤트에 추가로 갖는 조건, 속성을 지정

```html
<div id="app">
        <button v-on:click="logText">click me</button>
        <input type="text" v-on:keyup.enter="logText">
        <button>add</button>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        new Vue({
            el: '#app',
            methods: {
                logText: function(){
                    console.log('clicked');
                }
            }
        })
    </script>
```

`v-on:keyup.enter`의 효과

* 입력하고서 사용자가 엔터를 쳤을 때 마치 add를 클릭해 이벤트를 발생한 시킨것 같은 효과를 준다.



### watch

* watch에 지정한 vue의 속성값이 변경했을 때 function을 실행시킬 수 있다.
* 매번 실행되는게 부담스러운 로직, 데이터 요청에 적합하다

`computed`: 로직이 실행될 때 기준이 data의 값이다. validator나 간단한 텍스트 연산

`watch`: 



### computed 속성을 이용한 클래스 코드 작성 방법

#### computed 속성 이용하지 않은 방법

* `v-bind:class={style 클래스속성이름: Vue의data}`: Vue 데이터 값이 True면 style 클래스가 적용됨

```html
<style>
  .warning {
    color: red;
  }
  </style>
</head>
<body>
  <div id="app">
    <p v-bind:class="{ warning: isError }">Hello</p>
  </div>
  
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script>
    new Vue({
      el: '#app',
      data: {
        // cname: 'blue-text',
        isError: false
      },
    });
  </script>
</body>
```

#### computed 속성 이용

* computed로 값을 바뀌게 하면 html의 v-bind:class 구문을 더 깔끔하게 만들수 있다.
  * ex) 메소드 호출해서 Vue data가 바뀌도록한다

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <style>
  .warning {
    color: red;
  }
  </style>
</head>
<body>
  <div id="app">
    <p v-bind:class="errorTextColor">Hello</p>
  </div>
  
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script>
    new Vue({
      el: '#app',
      data: {
        // cname: 'blue-text',
        isError: false
      },
      computed: {
        errorTextColor: function() {
          // if (isError) {
          //   return 'warning'
          // } else {
          //   return null;
          // }
          return this.isError ? 'warning' : null;
        }
      }
    });
  </script>
</body>
</html>
```



### Vue CLI 소개

* CLI: Command Line Interface

* 명령어 실행 도구

#### 설치

* [Vue CLI](https://cli.vuejs.org/guide/)
* Installation
* node 10버전 이상, npm 6버전 이상

```
node -v
npm -v
npm install -g @vue/cli
```

### CLI 도구 설치시 문제점과 설치 장소

* 에러 발생은 npm을 설치한 위치에 대한 권한이 없기 때문에 발생
  * `sudo npm install -g @vue/cli



### CLI 2.x와 3.x의 차이점/ 프로젝트 생성 및 서버 실행

Vue CLI 2.x

* vue init '프로젝트 템플릿 유형' '프로젝트 폴더 위치'
* vue init webpack-simple '프로젝트 폴더 위치'

Vue CLI 3.x

* vue create '프로젝트 폴더 위치'

```bash
vue create vue-cli
cd vue-cli
npm run serve
```



### CLI로 생성한 프로젝트 폴더 구조 확인 및 main.js 파일 설명

* npm(node package manager): package.json에서 라이브러리 설정

##### npm run serve

`npm run serve`: package.json에서 "serve": "vue-cli-service serve"라고 정의되어있다. 

vue-cli-serve serve 라는 명령어를 serve라고 맵핑한것

`npm run serve`은 index.html 파일을 실행한다. `<!-- *built files will be auto injected* -->` 구문에는 src폴더의 main.js, App.vue를 포함한 다른 파일들이 하나의 파일로 주입된다. 내부적으로 웹팩으로 실행된다.

##### main.js

컴포넌트의 내용을 다른 파일로부터 불러와서 렌더를 하는 code

```js
import Vue from 'vue'
import App from './App.vue'

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
}).$mount('#app')

new Vue({
  el: '#app',
  render: h => h(App),
})
```

mount를 '#app'에 붙이겠다는 것은 아래의 코드와 같다.

render: 내부적으로 사용하는 함수이자 사용자(개발자)도 사용하는 함수 템플릿 속성을 지정(사용)했을 때 render가 실행된다.

### 싱글 파일 컴포넌트 소개 및 적용 방법

vue 구문 자동완성: scf + tap

##### App.vue 형태

```vue
<template>
    <!-- HTML -->
    <div>header</div>
</template>

<script>
export default {   // es6문법
    
    // Javascript
    methods: {
        addNum: function() {

        }
    }
}
</script>

<style>
    /* CSS */
</style>

```

* 기존에 배운 방법의 코드

```
let appHeader = {
    template: '<div>header</div>',
    methods: {
        addNum: function() {

        }
    }
}
```



### App.vue와 HellowWorld.vue 설명

* 표기법 3가지

```
<!-- 컴포넌트 명명법 종류 -->
<hello-world></hello-world> // 파일바로가기 하려면 이 명명법(케파키크) 사용해야함
<HelloWorld></HelloWorld> // 파스칼케이스
// 두 표기는 똑같다
<HelloWorld/> // 좀더 진보된 표현
```

```
components: {
    HelloWorld,
    'hello-world': HelloWorld
  }
```

* 컴포넌트를 등록하는 두 명명법은 같은 동작을 한다.

> HelloWorld.vue

```vue
<script>
// let appContent = {
//   props: ['propsdata']
// }

export default {
  // 인스턴스 옵션 속성
  name: 'HelloWorld',
  props: {
    msg: String,
    //props: ['msg']
  }
}
</script>

// let appContent = {
//   props: ['propsdata']
// }
```

* export default의 props 속성 정의와 주석에 쓰인 props정의는 같은 동작을 한다



### 싱글 파일 컴포넌트에 배운 적용하여 개발 시작하기

[배민찬은 Vue를 어떻게 사용하나요?](<http://woowabros.github.io/experience/2018/06/07/vue-story-of-baminchan.html>)

* template에서 최상단의 root html 태그는 하나만 있어야 한다. root에 html 병렬 불가
  * 여기서 root는 Vue의 ROOT가 아니라 template안에서 root를 말한다

* data 정의 방법의 변화

여러개의 component를 재사용할 가능성이 높다. 여러개의 컴포넌트에서 동일한 값을 참조하면 안되기 때문에 

data: {}라는 객체가 아니라 data: function(){}으로 새객체를 반환하는 형태로 만들어줘야한다.

### 싱글 파일 컴포넌트 체계에서 컴포넌트 등록하기

* 컴포넌트이름은 파스칼방식으로 두단어 이상으로 조합

AppHeader.vue의 내용을 App.vue에서 사용하려면 App.vue에서 AppHeader.vue를 import하고 components에 등록한다. (import할때 파일위치는 확장자까지 붙여준다.)

### 싱글 파일 컴포넌트에서 props 속성 사용하는 방법

상위 컴포넌트인 App.vue에서 하위 컴포넌트 AppHeader.vue에 str이란 데이터를 내리는 경우

`<!-- *<app-header v-bind:프롭스 속성이름="상위 컴포넌트의 데이터이름"></app-header>* -->`  사용

> App.vue

```html
<app-header v-bind:propsdata="str"></app-header>
```

> AppHeader.vue

```vue
<script>
export default {
    props: ['propsdata']
}
```

### SFC에서 event emit 구현하기

하위 컴포넌트에서 emit으로 event를 발생시키면 상위 컴포넌트에서 event에 대한 method실행으로 데이터를 조작한다. 상위 컴포넌트에서 하위 컴포넌트를 실행하는 영역안에 있는 태그의 속성에 `v-on: `을 지정한다.

> AppHeader.vue

```
methods: {
        sendEvent: function() {
            this.$emit('renew') // 이벤트를 올린다.
        }
    }
```

> App.vue

```vue
<app-header 
      v-bind:propsdata="str"
      v-on:renew="renewStr">
      </app-header>


data: function() {
return {
str: Header
}
}
methods: {
    renewStr: function() {
      this.str='hi';
    }
  }
```



### 프로젝트 생성 및 마크업 작업

프로젝트 생성

```bash
vue create vue-form
cd vue-form
npm run serve
```

* App.vue 내용 제거 scf+tap
* HelloWorld.vue 삭제

마크업 작업

> App.vue

```vue
<form action="">
    <div>
      <label for="username">id: </label>
      <input type="username" type="text">
    </div>
    <div>
      <label for="password"></label>
      <input id="password" type="text">
    </div>
  </form>
```

* label의 for는 어떤 태그를 위한 label을 의미하는지 작성
* input의 id는 앞의 for의 이름과 맞춰주면 label과 input은 한쌍이 된다.

### v-model 속성과 submit 이벤트 처리

* 버튼을 눌렀을 때 새로고침되는 문제

button의 type이 "submit"이면 event가 form 태그로 올라간다. 그래서 form 태그에서 

`v-on:submit="submitForm"`으로 submit 이벤트를 form태그에서 받았을 때 submitForm 메소드를 실행한다.

submit을 하면 어떤 페이지로 넘어가는 동작을 하기때문에 새로고침이 된다. 

> 기존 JS의 새로고침 방지

```vue
<form v-on:submit="submitForm">
    <div>
      <label for="username">id: </label>
      <input id="username" type="text" v-model="username">
    </div>
    <div>
      <label for="password">pw: </label>
      <input id="password" type="password" v-model="password">
    </div>
    <button type="submit">loin</button>
  </form>

submitForm: function(event) {
      event.preventDefault();
      console.log(this.username, this.password);
    }
```

event인자는 submit이벤트를 받고, 이 인자가 preventDefault()를 실행하면 새로고침이  일어나지 않는다.

> Vue의 새로고침 방지

```vue
<form v-on:submit.prevent="submitForm">
    <div>
      <label for="username">id: </label>
      <input id="username" type="text" v-model="username">
    </div>
    <div>
      <label for="password">pw: </label>
      <input id="password" type="password" v-model="password">
    </div>
    <button type="submit">loin</button>
  </form>

    submitForm: function() {
      //event.preventDefault();
      console.log(this.username, this.password);
    }
  }
```

### axios를 이용한 데이터 전송 및 form 구현

````bash
npm i axios
````

`npm`: npm 라이브러리를 현재 프로젝트에 다운받는다.

```vue
<script>
    import axios from 'axios';
    export default {
        methods: {
    submitForm: function() {
          //event.preventDefault();
          console.log(this.username, this.password);
          let url = 'https://jsonplaceholder.typicode.com/users';
          let data = {
            username: this.username,
            password: this.password,
          }
          axios.post(url, data)
          .then(function(response){
            console.log(response);
          })
          .catch(function(response){
            console.log(response);
          });
        }
        }
    }
</script>
```



### 수업정리 및 향후 학습 방향 안내

- Reactivity: 데이터 변화를 Vue에서 감지해서 화면에 나타낸다

* 인스턴스
* 컴포넌트: 화면의 영역을 구분해서 개발하는 방식
* 컴포넌트 통신
  * props
  * event emi
* HTTP 통신 라이브러리 (axios)
* 템플릿 문법
  * 데이터 바인딩: 데이터를 화면에 엮어 나타냄
  * 뷰 디렉티브 (v-) : 화면을 제어하기 위한 문법
* Vue CLI
* 싱글 파일 컴포넌트

###### [Vue.js 공식문서](<https://vuejs.org/v2/guide/index.html>)

###### [Vue.js 스타일 가이드](<https://kr.vuejs.org/v2/style-guide/index.html>)

###### [Vue.js cookbook](<https://vuejs.org/v2/cookbook/index.html>)

###### [Vuex 공식 문서](<https://vuex.vuejs.org/kr/>)


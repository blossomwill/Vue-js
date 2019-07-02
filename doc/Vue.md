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


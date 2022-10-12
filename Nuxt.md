# Nuxt.js

## 특징

- React의 next.js보다 DX(Developer experience)가 훨씬 좋다.
- 폴더를 통해 기능을 인식하기 때문에 폴더명을 정해진대로 입력해야 한다.
- Vue.js처럼 app.vue를 이용할 수도 있고, app.vue 없이 구성하면 `pages/index.vue`를 홈페이지로 삼거나, `layouts/default.vue`를 기본화면으로 삼는다.(하지만, app.vue의 이용을 권장하는 것이 전역 css 등의 관리가 훨씬 용이하다.)
- vue 페이지의 구성은 vue.js와 같으나 여러 편의성을 갖춘 문법이 추가되었다.



## 시작하기

1. 프로젝트 생성
   ```bash
    npx nuxi init <PROJECT_NAME>
   ```

2. 의존성 다운로드
   ```bash
   yarn install or npm install
   ```

3. 개발 서버 실행
   ```bash
   yarn dev or npm run dev
   ```

   

## .nuxt

`.nuxt`디렉토리에는 개발환경/배포환경으로 빌드한 결과물들이 전부 들어가 있다. 모듈, SSR, Router 설정 등이 전부 이곳에 빌드되어 작동하므로 빌드가 꼬인 경우 `.nuxt/`를 지우고 다시 빌드할 것.
 `node_modules/`와 마찬가지로 `.gitignore`에서도 제외하는 것이 좋다.

## App.vue

- Vue.js의 App.vue와 비슷한 역할을 수행한다.
- App.vue 컴포넌트가 없더라도, Nuxt.js는 layouts/default.vue와 pages/index.vue를 각각 기본 페이지/레이아웃으로 인식한다.

```vue
<template>
  <div>
    <!-- layouts/default -->
    <NuxtLayout />
    <!-- pages/index -->
    <NuxtPage />  
  </div>
</template>
```



## pages

- app.vue에 `<NuxtPage>`태그 작성시 하위에 들어가는 폴더명과 파일명이 라우팅 경로로 자동 지정됨.
- index.vue는 홈페이지 역할을 한다.({ path: '/' })
- Vue.js는 실제로는 페이지가 하나이지만 브라우저의 히스토리 API를 이용해서 컴포넌트를 바꿔치기 하는 방식이었다면(url 변경으로 바꿔치기), Nuxt는 진짜로 pages 내의 컴포넌트가 하나의 개별적인 페이지이다.(코드 스플리팅)
- 즉, 코드 스플리팅으로 인해 SSR 방식이 적용되었으며 Vue.js의 CSR처럼 파일을 전부 불러오는게 아닌 페이지별로 불러오게 된다. CSR의 새 페이지 로딩시 페이지 깜빡임 등을 방지하기 위해 다음에 옮겨갈 페이지들을 미리 가져오는 기능도 적용되어 있다.

### dynamic routes

nuxt3의 Dynamic Routes는 `대괄호[]`를 이용하여 설계한다.

```
pages/
	[user]/
		post/
			[id].vue
```

위처럼 설계된 pages 컴포넌트가 있다고 가정하자. 위 컴포넌트는 아래와 같은 구조를 가지게 된다.

- route.name = 'user-post-id'
- url = '/:user/post/:id' `예시 : localhost:3000/jamesbond/post/13324567081`

더불어, 중첩 라우트(nested routes)를 고려하여 이와 같이 설계해도 같은 방식으로 동작한다.

```
pages/
	[user]/
		post/
			[id]/
				index.vue // === [id].vue
				something.vue
```



## layouts

### 특징

- default.vue를 기본 레이아웃으로 삼으며, app.vue를 사용할 경우 `<NuxtLayout />`으로 불러올 수 있다. 
- app.vue를 사용하지 않는 경우 자동으로 default.vue를 불러오며, 내부에 `<NuxtPage />`태그를 작성해야 페이지를 출력한다.
- Default 이외의 레이아웃은 layouts 디렉토리에 원하는 명칭으로 생성 후, 원하는 page에서 예시대로 불러온다.

### 예시

```vue
<template>
  <div>
    <nav>
      <!-- Vue.js의 router-link -->
      <nuxt-link to="/">Home</nuxt-link> | 
      <nuxt-link to="/profile">Profile</nuxt-link> | 
      <nuxt-link to="/signup">Sign Up</nuxt-link>
    </nav>
    <!-- Pages / Vue.js의 router-view -->
    <!-- app.vue 사용시 NuxtPage는 app.vue 추천 -->
    <!-- Grid 등으로 배치 조절할 때는 여기서 사용도 Ok! -->
    <NuxtPage />
  </div>
</template>
```

app.vue 사용시 예시

```vue
<!-- app.vue -->
<template>
	<NuxtLayout />
	<NuxtPage />
</template>
```



Custom layouts

```vue
<template>
  <div>
  </div>
</template>
<script>
definePageMeta({
  // 특정 layout 사용
  layout: 'custom',
  // layout 미사용
  layout: false
})
export default {
  setup() {
  },
}
</script>
```

## composables

### 작성예정

각 페이지마다 필요한 store를 import 했어야 했다면, composables directory를 이용하면 전역 import를 한 효과를 얻을 수 있다.

## route middleware

### 개요

라우트 미들웨어(Route Middleware)는 페이지나 레이아웃이 렌더링 되기 전에 호출되는 커스텀 훅(Hook)으로, 비로그인시 특정 페이지로 이동 등의 기능을 쉽게 구현할 수 있게 도와준다.

MVC 구조에 빗대어 설명하자면, View와 Model 사이에서 데이터와 화면을 연결해주던 컨트롤러의 역할과 비슷하게, 화면의 렌더링 전에 데이터를 확인 후 그 결과에 맞는 라우트로의 이동을 해준다고 볼 수 있다.

### 특징

- 페이지를 호출할 때 작동하므로 라우트 미들웨어는 `pages/`내에서 선언하여 불러온다.
- Nuxt2와는 달리 매개변수로 router의 `to`와 `from`만을 갖는다.
- 상태 관리 라이브러리의 경우, Pinia는 pinia store를 import한 후, setup에서 사용하던 것과 동일하게 사용할 수 있다.

### 선언

선언방식은 크게 세 가지가 있는데, inline 방식 / middleware directory 내 정의 / plugins 내 정의 방식이 있다.

1. inline
   인라인 방식은 말 그대로, vue page에서 인라인으로 미들웨어를 작성하는 방식이다. 기본적으로 **pages 내 vue**에 작성해야 한다.

   ```vue
   <template></template>
   <script setup>
   	definePageMeta({
     // This is an example of inline middleware
     middleware: () => {
       console.log('inline 미들웨어입니다.')
       return false
     }
   });
   </script>
   ```

2. middleware directory
   **가장 추천하는**방식이며, middleware라는 디렉토리를 root폴더에 생성한 후, 그 하위에 파일을 작성한다.
   파일명은 반드시 **kebab-case**여야 하며, `FILE_NAME.global.ts(js)`형식으로 작성한 경우 **따로 호출할 필요 없이** 어느 페이지에서나 작동한다.(글로벌 선언)

   ```javascript
   // global route middleware
   // middleware/always-run.global.ts
   // vue-router의 to, from을 기본 매개변수로 갖고 있다.
   export default defineNuxtRouteMiddleware((to, from) => {
     console.log('언제 어디서나 작동하고 있는 global middleware!');
   });
   ```

   ```javascript
   // name route middleware
   // middleware/auth.js(js, ts 상관없음)
   import { useUsersStore } from '~/stores/users';
   
   export default defineNuxtRouteMiddleware((to, from) => {
     const usersStore = useUsersStore();
     if (!usersStore.state.me) {
       // route 반환
       return '/profile';
     }
   });
   ```

   

3. plugins 내 정의
   **가장 비추천하는** 방식으로, 기타 플러그인들과 역할이 헷갈릴 수 있으며, named route middleware의 경우 정상작동하지 않는 경우가 있어 불편하다.

   ```javascript
   // plugins/middleware-test.js
   export default defineNuxtPlugin(() => {
     // global: true => global 선언
     addRouteMiddleware('global-test', () => {
       console.log('plugin을 통해 선언한 global middleware!');
     }, { global: true });
   	
     // named
     // addRouteMiddleware('named-test', () => {
     //   console.log('plugin의 named middleware 작동');
     // });
   });
   ```



### 호출

호출은 언급한 대로, `pages/` 내 vue page에서 호출하며, `global`의 경우에는 따로 호출하지 않아도 자동 작동한다.

```vue
<script setup>
	definePageMeta({
    middleware: 'auth'
  });
  // or 여러 미들웨어를 한번에 부르기도 가능하다.
  definePageMeta({
    middleware: ['auth', () => {
      console.log('inline middleware');
      return '/profile';
    }];
  });
</script>
```

참고로 **호출 순서**는, `middleware directory -> plugins directory -> inline(vue이므로 가장 늦다.)` 순으로 호출된다.

### 활용

기존에 `app.vue`에서 비로그인을 했다고 가정해보자. middleware, inline 그리고 app.vue 중 어떤 쪽의 라우터가 먼저 호출될까?

```vue
<script setup>
  import { useUsersStore } from '~/stores/users';
	// app.vue
  const usersStore = useUsersStore();
  if (!usersStore.state.me) {
    // router.push({ name: 'intro' });
    router.replace('/intro');
  }
</script>
```

```vue
<script setup>
	// pages/index.vue
  definePageMeta({
    middleware: () => { console.log('inline') }
  })
</script>
```

```javascript
// middlewares/auth.js
import { useUsersStore } from '~/stores/users'
export default defineNuxtRouteMiddleware((to, from) => {
  const usersStore = useUsersStore();
  if (!usersStore.state.me && to.path !== '/intro') {
    return '/intro';
  }
});
```

정답은,

1. Middleware directory 호출
2. pages 내 Inline 호출
3. app.vue 내 코드 호출

이렇게 `app.vue`에서 코드를 호출하더라도, pages내에 선언된 middleware보다 늦게 호출되는 문제가 생기는데, 이는 app.vue 내의 코드는 결국 렌더링 중에 호출되기 때문이다. 따라서, 비로그인 등의 설정,혹은 다국어 지원시 해당 언어가 없을 때 특정 언어를 출력하는 로직 등을 구현할 때는 렌더링 전에 실행될 수 있도록 라우트 미들웨어 내에 구현하는 것이 좋다.



## public

변경되지 않을 파일들을 넣어두는 곳으로, 대표적으로 `favicon.ico` 등이 여기 들어가며, 자동 적용된다. 그 이외에 `public/favicon.ico` 등의 경로가 자동생성된다.

- 경로 입력시 `/public`의 생략이 가능
  ```html
  <!-- public 디렉토리 사용시 -->
  <img src="/my-image.png">
  <!-- assets 사용시 -->
  <img src="~/assets/my-image.png">

### Favicon

- `public` 디렉토리에 `favicon.ico` 파일을 넣어주기만 하면 자동으로 파비콘이 적용된다.

## utils

> 되도록이면 [Composables](#Composables)를 사용할 것!

특정 기능을 수행하는 JS function들을 담는 디렉토리이다. 주로 여러 컴포넌트에서 반복적으로 사용하게 될 **비API** 함수가 있는 경우 utils에 담게 된다.

`Composables`와 비슷하며  Composables가 template 등의 화면 구성 요소 + 컴포넌트 내 특정 변수의 전달도 수행한다면, utils는 단순히 특정 기능을 수행하는 함수들의 구성에 치중한다고 볼 수 있다. Vue3 / Nuxt3에서는 Composition API로도 작성이 가능하다.

하지만 Composables가 나오면서 사실상 utils를 활용할 경우는 극한되었다고 볼 수 있으며, 대부분의 코드가 composables에서 대체가 가능하다.

### 예시

대표적인 예로 `Eventbus`가 있다.

```javascript
// ~/utils/bus.js
export default function useEventBus() {
  let eventsListeners = {};
  /**
   *
   * @param {string} events event name
   * @param {function} handler event handler
   * @param {boolean} priority priority
   * @returns object
   */
  function $on(events, handler, priority) {
    if (typeof handler !== 'function') return;
    const method = priority ? 'unshift' : 'push';
    events.split(' ').forEach(event => {
      if (!eventsListeners[event]) eventsListeners[event] = [];
      eventsListeners[event][method](handler);
    });
    return;
  }

  function $off(events, handler) {
    if (!eventsListeners) return self;
    events.split(' ').forEach(event => {
      if (typeof handler === 'undefined') eventsListeners[event] = [];
      else if (eventsListeners[event]) {
        eventsListeners[event].forEach((eventHandler, index) => {
          if (eventHandler === handler) eventsListeners[event].splice(index, 1);
        });
      }
    });
    return;
  }

  function $trigger(...args) {
    if (!eventsListeners) return;
    let events;
    let data;
    let context;
    if (typeof args[0] === 'string' || Array.isArray(args[0])) {
      events = args[0];
      data = args.slice(1, args.length);
      context = self;
    } else {
      events = args[0].events;
      data = args[0].data;
      context = args[0].context || self;
    }

    data.unshift(context);
    const eventsArray = Array.isArray(events) ? events : events.split(' ');
    eventsArray.forEach(event => {
      if (eventsListeners && eventsListeners[event]) {
        eventsListeners[event].forEach(eventHandler => {
          // this 지정 후 실행
          eventHandler.apply(context, data);
        });
      }
    });
    return;
  }

  return {
    eventsListeners,
    $on,
    $off,
    $trigger // 혹은 $emit으로 명하기도 함.
  }
}
```

- 호출 방법 : 호출방법은 기본적으로 plugin과 매우 유사하다. 하지만, tag내에서 inline으로 즉시 불러올 수는 없고, 반드시 setup에서 호출 후 return을 한 후에 사용이 가능하다.

```vue
<template></template>
<script setup>
	import useEventBus from 'PATH/bus.js';
  // 구조분해할당
  const { $on, $off, $trigger } = useEventBus();
</script>
```



## useHead

- useHead Composable을 사용하면 `<head>`태그 설정이 가능하다.
- script의 setup 내부에 선언하여 사용한다.
- layouts을 사용 중일때, layouts에도 useHead를 사용하고 있다면, pages에 선언한 useHead가 우선 적용된다.

### 예시

```vue
<template>
  <div>
  </div>
</template>
<script>
export default {
  setup() {
    // Nuxt function : <head></head> settings
    useHead({
      // <head> 내 <title>태그 설정
      title: 'Profile'
    });
  },
}
</script>
```

### Dynamic

- head명을 동적으로도 설정할 수 있다.

```vue
<!-- layouts/default.vue -->
<!-- 반드시 layouts에 설정할 필요는 없고, 원하는 곳에 설정 가능(app.vue 추천) -->
<template>
  <Html>
    <Head>
      <Title>{{ dynamic }}</Title>
    </Head>
  </Html>
</template>
<script>
  export default {
    setup() {
      useHead({
      // as a string,
      // where `%s` is replaced with the title
      titleTemplate: '%s - Site Title',
      });
      const route = useRoute();
      return {
        dynamic: computed(() => route.name)
      }
    },
	}
</script>

<!-- console 내 결과 -->
<head>
  <!-- index에 usehead({ title:'' }) 설정이 없다면 자동으로 파일명을 title로 인식-->
  <title>index - Site Title</title>
</head>
```

그렇다면 pages에 `useHead({ title: 'profile pageeee' })`을 설정하고, app.vue에 위의 dynamic을 설정했다면 어떻게 될까?

```vue
<head>
  <!-- 이처럼 %s 부분을 pages 컨텐츠가 덮어씌운다. -->
	<title>profile pageeee - Site Title</title>
</head>
```



## Nuxt.config.js(Nuxt.config.ts)

- 공통으로 사용하는 설정, 객체 등을 적용하는 데 이용한다.

```javascript
// ~/nuxt.config.ts
import { defineNuxtConfig } from 'nuxt'

// https://v3.nuxtjs.org/api/configuration/nuxt.config
export default defineNuxtConfig({
  modules: [
    // 전역 import할 npm 모듈(패키지, 라이브러리)
    [
    '@pinia/nuxt',
    {
      // 전역 import
      autoImports: [
        'defineStore',
        'storeToRefs'
      ]
    }
    ]
  ],
  // 전역 css
  css: [
    '~/assets/css/soonsak.css',
    '@fortawesome/fontawesome-svg-core/styles.css'
  ],
});

```

## Nuxt와 ESLint 연결

> ESLint는 팀 프로젝트 혹은 개인 프로젝트에서 코드의 일관성을 유지시키는데 도움을 주고, 동일한 코딩 컨벤션 안에서 코드를 작성할 수 있도록 도와주는 유용한 정적 코드 분석 도구이다.

1. 개발용(원한다면 전체 적용도 가능하지만, eslint를 사용자가 요구할 경우는 없으므로 개발용으로 설친한다.)으로 eslint와 본인이 사용하는 언어/프레임워크에 맞는 플러그인을 설치한다.
   ```bash
   # -D : 개발자용, eslint-plugin-vue: vue전용 eslint 플러그인
   npm i -D eslint eslint-plugin-vue @vue/eslint-config-standard
   ```

   ```json
   // package.json
   {
     ...,
     // 개발자용 패키지
     "devDependencies": {
     	"@vue/eslint-config-standard": "^8.0.1",
     	"eslint": "^8.23.1",
     	"eslint-plugin-vue": "^9.4.0",
     	"nuxt": "3.0.0-rc.9"
   	}
   }
   ```
   
2. ROOT폴더에 `.eslintrc`를 작성한다.
   ```json
   {
     "root": true,
     "parserOptions": {
       "ecmaVersion": "latest",
       "sourceType": "module"
     },
     "env": {
       "browser": true,
       "node": true,
     },
     "extends": [
       // 치명적 오류 발생 가능한 코드 검증
       "plugin:vue/vue3-essential",
       "plugin:vue/"
     ],
     "plugins": [
       
     ],
     "rules": {
       
     },
     "globals": {
       
     }
   }
   ```
   
   1. `root: T/F`: 여러 세부 프로젝트가 내부에 있는 경우, 각 프로젝트 별로 린트 설정이 다를 수 있는데, root 옵션을 true로 주면 최상위 lint로 인식하여 하위 lint가 더이상 상위 폴더로 올라가면서 탐색하지 않는다.
   
   2. `plugins: ['PLUGIN_NAMES']`: 기본 규칙 외에 각 생태계에 맞는 추가적인 규칙을 추가해서 사용할 수 있다. 플러그인은 **규칙 활성화를 시키는 것은 아니며**, 단순히 규칙을 설정할 수 있도록 규칙 목록을 추가하기만 한다. 설정은 extends 혹은 rules를 이용하여 직접 설정해야 한다. 

      ```bash
      # React 설정 예시
      npm i -D eslint-plugin-import eslint-plugin-react
      ```
   
      ```json
      "plugins": [
        "import",
        "react"
      ]
      ```
   
   3. `extends: ['EXTENDS_NAMES']`: Google, Facebook, Airbnb 등의 기업들 혹은 기타 npm 패키지 개발자들이 설정해둔 린트를 활성화할 수 있다. 이들이 미리 설정해둔 규칙이 활성화된다.
   
      ```json
      // React 설정 예시 계속
      "extends": [
        "plugin:import/recommended",
        "plugin:react/recommended"
      ]
      ```
   
      ```bash
      # Vue airbnb 적용 예시
      npm install airbnb-confgi-airbnb -D
      npm install eslint-plugin-import -D
      # prettier 설치
      # npm i -D prettier
      # npm i -D eslint-config-prettier eslint-plugin-prettier # ESLint와 통합 적용
      ```
   
      ```json
      "extends": [
        "plugin:vue/essential",
        "airbnb-base",
        "@vue/prettier",
        // prettier 적용. 맨 하단에 와야함에 유의.
        "plugin:prettier/recommended"
      ]
      ```
   
   4. `rules: {.KEY: VALUE }`: extends에서 적용된 규칙들에 우선하여 규칙을 변경하거나 자신이 직접 규칙을 작성하고자 할 때 사용한다.이 규칙들은 위에서 설정한 extends 혹은 plugins 안에 정의되어 있어야 한다.
      옵션은 아래와 같이 설정할 수 있다.
   
      - `"off" 또는 0` : 규칙을 비활성화한다.
      - `"warn" 또는 1`: 규칙을 활성화하고, 규칙을 어긴 경우 경고한다.(Exit Code는 발생하지 않음.)
      - `"error" 또는 2`: 규칙을 활성화하고, 규칙을 어긴 경우 에러를 발생시킨다.(Exit code가 일어난다.)
   
      ```json
      // Vue.js 개인 설정 예시
      "rules": {
        // 둘 다 똑같이 동작한다.
        "semi": "off",
        "semi": 0,
        
        "space-before-function-paren": [
              "error",
              {
                "anonymous": "always",
                "named": "never"
              }
            ],
      }
      ```
   
   5. `env: {KEY: T/F}`: 각 실행환경(Runtime)에서 사용하는 전역 객체(`e.g.Browser의 windows`)를 통해 어떤 실행환경에서 접근 가능한 지 명시하여 접근하는 변수에서 발생하는 오류를 방지할 수 있다.(env에 선언하지 않은 실행환경의 전역객체에 접근한 경우 오류가 발생한다.)
   
      ```json
      "env": {
        "browser": true,
        "node": true
      }
      ```
   
   6. `parser`와 `parserOptions`: ESLint는 기본적으로 순수 자바스크립트 코드만을 분석할 수 있으므로, 자신의 개발환경에 맞는 파서를 사용하도록 설정해야 한다.
   
      ```json
      // TS와 JSX 설정 예시
      "parser": "@typescript-eslint/parser",
      "parserOptions": {
        "sourceType": "module",
        "ecmaFeatures": {
          "jsx": true
        },
        "ecmaVersion": "latest"
      }
      ```
      
   6. `overide: [{ KEY: VALUE }]`: 프로젝트 내 일부 파일에만 다른 설정을 적용하고 싶은 경우 사용한다.
   
      ```json
      // Typescript용 설정 적용 예시
      "overrides": [
        {
          "files": "**/*.+(ts|tsx)",
          "parser": "@typescript-eslint/parser",
          "plugins": ["@typescript-eslint"],
          "extends": ["plugin:@typescript-eslint/recommended"]
        }
      ]
      ```
      
   6. `globals: {KEY: T/F}`: Vue 등에서 전역에 import하여 하위 컴포넌트에서 import가 필요없는 함수들이 있을 것이다. 하지만 ESLint는 `no-undef(undefined 에러)`를 일으키는데 이를 방지할 때 쓰는 것이 globals option이다. 선언한 변수/함수들을 마치 전역 변수인 것처럼 판단한다.
   
      ```json
      "globals": {
        // false로 처리한 것은 컴포넌트 내부에 import 되어 있지 않아도 no-undef 에러를 발생시키지 않는다.
        "ref": false,
        "reactive": false,
        "toRefs": false,
        "useHead": false,
        "useRoute": false,
        "definePageMeta": false
      }
      ```
      
      
   
2. ROOT DIRECTORY에 `.eslintignore` 생성
   검색 대상에서 제외할 디렉토리/파일을 설정합니다.(위치는 현재 위치 기준)
   
   ```
   assets/
   package.json
   package-lock.json
   tsconfig.json
   README.md
   ```
   
2. `package.json`에 명령어 설정
   설정한 ESLint로 분석할 코드의 범위를 설정한다.
   
   ```json
   // package.json
   {
     ...,
     "scripts": {
     	"build": "nuxt build",
     	"dev": "nuxt dev",
     	// 모든폴더/모든파일 검사
     	"lint": "eslint '**/*'"
   	}
   }
   ```
   
2. 실행
   결과는 콘솔창에서 확인할 수 있다.
   
   ```bash
   npm run lint
   ```
   



## 상태 관리 라이브러리의 적용

> Vue.js의 대표적인 상태 관리 라이브러리로는 Vuex와 Pinia가 있다. Pinia는 OptionsAPI와 CompositionAPI 방식 모두의 작성법을 지원하는데, 아래에선 CompositionAPI 방식의 작성법을 소개하고 있다.
> 자세한 사용 방법은 [Link](https://github.com/wonhyeokjung/pinia-n-vuex)참조

Vuex는 Vue.js 초창기에 배포되어 높은 범용성을 가지고 있지만, Vue3의 Composition API와의 호환성이 좋지 못하고 Nuxt3에서 제대로 지원하지 않아 Vue3를 겨냥해 나온 Pinia를 상태 관리 라이브러리로 사용한다.

### Installation

```bash
# pinia를 설치하지 않으면 에러가 발생한다.
npm install pinia @pinia/nuxt
```

### nuxt.config.ts

autoImports 옵션을 사용하면 각 컴포넌트에서 따로 import할 필요 없이 자동으로 import를 해준다.

```typescript
import { defineNuxtConfig } from 'nuxt';

export default defineNuxtConfig({
  // 전역 import할 npm modules(packages, libraries)
  modules: [
    [
      '@pinia/nuxt',
      {
        // 전역 import할 변수/함수
        autoImports: [
          'defineStore',
          ['defineStore', 'definePiniaStore'], // import { defineStore as definePiniaStore } from 'pinia'
        ]
      }
    ] // @pinia/nuxt끝
  ] // modules끝
})
```

### Define a Store

```javascript
// ~/stores/STORE_NAME.js(.ts)
export const useIndexStore = defineStore('index', () => {
  const count = ref(0);
  return {
    count
  }
});
```

### Usage

```vue
<template>
	<div>
    <!-- 특정값이 아닌 값 전체를 불러오는 경우 Nuxt3에선 콘솔상 에러가 발생할 수 있으므로 유의할 것. -->
    {{ indexStore }}
  </div>
</template>
<script>
	import { useIndexStore } from '~/stores/index';
  export default {
    setup() {
      const indexStore = useIndexStore();
      return {
        indexStore
      }
    }
  }
</script>
```

### result

```html
<html>
  <body>
    <div>
      <!-- $id는 store 정의시에 할당한 첫번째 인자이다.-->
      <!-- defineStore('$id', () => {}) -->
      { "$id": "index", "count": 0 }
    </div>
  </body>
</html>
```

### Pinia와 computed를 이용한 간단한 로그인 여부 확인

```vue
<template>
	<div>
    <div v-if="isLoggedIn">
      로그인함.
  	</div>
    <div v-else>
      로그인 요망.
  	</div>
  </div>
</template>
<script>
	export default {
    setup() {
      const usersStore = useUsersStore();
      const isLoggedIn = computed(() => usersStore.state.user);
    }
  }
</script>
```



## Plugin

### Nuxt2 Style

Nuxt2에선 하나의 js 파일에서 import로 불러온 여러 plugin을 동시에 적용하는 것이 가능했는데,

Nuxt3에선 위와 같은 Nuxt2의 스타일을 지원하지 않을 예정이다.(콘솔 상의 경고 메세지를 확인 가능) 그러므로 아래와 같은 방식은 지양해야 한다.

```javascript
// ~/plugins/something.js
export default function something {
  return 'hello world'
}

// ~/nuxt.plugin.ts
import something from '~/plugins/something'
export default defineNuxtPlugin((/* nuxtApp */) => {
  return {
    provide: {
      foo: something,
      something
    }
  }
});
```

### Nuxt3

- 한 파일에서 export까지 마무리하는 것을 기본으로 한다.
- 즉, 플러그인을 여러개 설정하면 각 플러그인에서 export하고, 그렇게 해도 정상작동한다.
- `<template>` 내에서 사용할 때는 따로 구조분해할당이 필요없지만, `<script setup>`내에선 구조분해할당으로 불러온 후 사용이 가능하다.
- 사용 시에 **$**(이름 충돌 방지)를 붙여야 불러올 수 있음에 유의한다.

```javascript
// ~/plugins/valid-check.js
function isValid(target, value, value2=undefined) {
  const errors = {
    email: /^[0-9a-zA-z]([-_.]?[0-9a-zA-Z])*@[0-9a-zA-Z]([-_.]?[0-9a-zA-Z])*\.[a-zA-Z]{2,4}$/.test(value) ? '' : '이메일 양식이 맞지 않습니다.',
    password: /(?=.*\d)(?=.*[a-z])(?=.*[^A-Za-z0-9])(?=.*[A-Z]).{8,}/.test(value) ? '' : '비밀번호는 대소문자, 숫자 그리고 특수문자 최소 하나씩 포함하고 8자 이상이어야 합니다.',
    confirmPassword: (value === value2) ? '' : '비밀번호가 일치하지 않습니다.'
  }

  return errors[target];
}

export default defineNuxtPlugin((/* nuxtApp */) => {
  return {
    provide: {
      // key값 할당
      validCheck: isValid,
      // key === value일시 생략도 가능
      isValid
    }
  }
});

```

```vue
<template>
	<!-- 호출명은 $ + 변수명임에 유의할 것. ->
	{{ $validCheck }}
	{{ $isValid('email', 'a@a.com') }}
</template>
<script setup>
	// 호출을 위해 구조분해할당을 할 필요가 없다.
</script>
```

```vue
<template>
	{{ foo }}
</template>
<script setup>
  // script 내 사용에선 구조분해할당이 필수이다.
	const { $isValid, $validCheck } = useNuxtApp();
  const foo = $isValid('email', 'a@a.com');
  // 혹은 const foo = $validCheck('email', 'a@a.com');
</script>
```



## Document is not defined Error

Nuxt는 기본적으로 SSR(Server-side Rendering)이므로 `window.document`가 존재하지 않아 이 문제가 발생한다. 이를 해결하기 위해서는 다음과 같은 방법이 있다.

1. process 객체 사용
   process 객체에는 `{.dev: false, server: false, client: false }`가 담겨있고, process.client가 true인지 확인 후 작업을 진행하면 정상적으로 document를 인식한다.

   ```vue
   <template>
   </template>
   <script>
   	if (process.client) {
       // ... Do something !
     }
   </script>
   ```

2. onMounted 사용

   `Vue.js의 onMounted`를 사용하면 정상적으로 document 인식이 가능하다.

   ```vue
   <template>
   </template>
   <script setup>
     onMounted(() => {
       import('something');
       console.log(window);
     })
   </script>
   ```

3. `<no-ssr>` tag 사용(Nuxt.js 3 지원여부 불명)

   `<no-ssr>`내부의 컴포넌트는 CSR로 렌더링되어 SSR에서 나타나는 문제점을 회피할 수 있다.

   내부에 선언한 컴포넌트에 `placeholder` slot을 사용하여 컴포넌트의 CSR이 끝날 때까지 보여줄 수도 있다.

   ```vue
   <template>
   	<!-- use a text as placeholder -->
   	<no-ssr placeholder="loading...">
     	<!-- this component will only be rendered on client-side -->
       <comments />
       <!-- use a text as placeholder -->
       <!-- no-ssr 내부의 placeholder slot에 내가 작성한 comments-placeholder 컴포넌트가 투입된다. -->
       <comments-placeholder slot="placeholder" />
     </no-ssr>
   </template>
   ```



## Font Awesome 사용하기

기존에 사용하던 cdn 등을 사용한 간단한 방식과는 다르게, 특정 패키지를 인스톨 후 플러그인을 작성해야한다. 이하 가이드는 `nuxt3` 전용이다.

1. 패키지 인스톨
   코어 패키지인 `@fortawersome/fontawesome-svg-core`를 인스톨 후 기타 사용할 예정인 아이콘들을 인스톨한다.

   ```bash
   npm i --save @fortawesome/vue-fontawesome@latest-3
   ```

2. 패키지 등록
   `nuxt.config.ts`에 전역css로 등록하고, `plugins/fontawesome.js`파일을 생성하고 이하 내용을 작성한다.

   ```javascript
   // nuxt.config.ts
   import { defineNuxtConfig } from 'nuxt'
   
   // https://v3.nuxtjs.org/api/configuration/nuxt.config
   export default defineNuxtConfig({
     // 전역 css
     css: [
       '@fortawesome/fontawesome-svg-core/styles.css'
     ],
   });
   ```

   ```javascript
   // plugins/fontawesome.js
   import { library, config } from '@fortawesome/fontawesome-svg-core';
   import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
   // 추가로 install한 패키지들을 import
   import { fas } from '@fortawesome/free-solid-svg-icons';
   import { far } from '@fortawesome/free-regular-svg-icons';
   import { fab } from '@fortawesome/free-brands-svg-icons';
   // This is important, we are going to let Nuxt worry about the CSS
   config.autoAddCss = false
   
   // You can add your icons directly in this plugin. See other examples for how you
   // can add other styles or just individual icons.
   // 싱글아이콘 패키지 등록
   library.add(fas, far, fab)
   
   export default defineNuxtPlugin((nuxtApp) => {
     // font-awesome-icon이란 컴포넌트 생성
     nuxtApp.vueApp.component('font-awesome-icon', FontAwesomeIcon, {})
   });
   ```

3. template 내 사용
   위에서 선언한 `font-awesome-icon`이란 태그를 사용하여 아이콘들을 불러온다.

   ```vue
   <template>
     <div>
       <font-awesome-icon icon="fa-solid fa-user-secret" />
     </div>
   </template>
   
   <template>
     <div>
       <!-- Reminder to bind the icon property with ":" -->
       <font-awesome-icon :icon="['fas', 'user-secret']" />
       <font-awesome-icon :icon="[true ? 'fa-solid' : 'fa-regular', 'fa-heart']" />
   
       <NuxtWelcome />
     </div>
   </template>
   ```

## Pinia 사용하기

피니아의 사용 역시 nuxt 내에 등록이 필요하지만, font awesome보다는 훨씬 간단하다.

1. 설치
   `pinia`와 `@pinia/nuxt`를 설치한다.
   ```bash
   npm install pinia @pinia/nuxt
   ```

2. 모듈 등록
   `nuxt.config.ts`내에 등록한다.
   한가지 팁이 있다면, `autoImports: string[]`를 설정해주면 전역 import로 처리되어 `defineStore`,` storeToRefs` 등을 계속 import해 줄 필요가 없다.

   ```javascript
   import { defineNuxtConfig } from 'nuxt'
   s
   export default defineNuxtConfig({
     modules: [
       [
       '@pinia/nuxt',
       {
         autoImports: [
           'defineStore',
           'storeToRefs'
         ]
       } // options
       ] // @pinia/nuxt
     ], // modules 끝
   });
   ```

   


# Vue

## 회원가입

### 에러 핸들링

에러 핸들링에는 watchEffect를 사용하여 에러 객체의 각 에러메세지를 투입하였다. 이후 조건문을 통하여 class를 태그에 할당하고, 클래스별로 색상, 디스플레이 등을 다르게 주어 에러 메세지를 출력하게 했다.

```vue
<template>
	<div :class="[errors.confirmPassword.value.length > 0 ? 'warning' : 'valid']">{{ errors.confirmPassword.value }}</div>
</template>
<script setup>
  watchEffect(() => {
    errors.email = /^[0-9a-zA-z]([-_.]?[0-9a-zA-Z])*@[0-9a-zA-Z]([-_.]?[0-9a-zA-Z])*\.[a-zA-Z]{2,4}$/.test(signUpForms.email) ? '' : '이메일 양식이 맞지 않습니다.';
    errors.password =  /(?=.*\d)(?=.*[a-z])(?=.*[^A-Za-z0-9])(?=.*[A-Z]).{8,}/.test(signUpForms.password) ? '' : '비밀번호는 대소문자, 숫자 그리고 특수문자 최소 하나씩 포함하고 8자 이상이어야 합니다.';
    // 일치 검사
    errors.confirmPassword = (signUpForms.password === signUpForms.confirmPassword) ? '비밀번호가 일치합니다.' : '비밀번호가 일치하지 않습니다.';
  });
</script>
<style>
  .warning {
    display: none;
    font-size: 1.2rem;
  }

  .valid {
    display: none;
  }
  input::placeholder {
    opacity: 0;
  }
  /** 플레이스홀더가 보이지 않을 때 === 글자가 입력된 경우 */
  input:not(:placeholder-shown) + .warning{
    display: block;
    color: red;
  }
</style>
```

### form 태그 내부의 v-on:invalid과 invalid의 처리

`<form>`태그 내부에서 invalid가 발생하게 되면, 자동으로 `이 필드를 채우세요`라는 메세지가 발생하게 된다. 하지만, 브라우저 별로 디자인이 다를 뿐만 아니라 디자인 자체도 뛰어난 편은 아니라 보통 메세지를 제거하게 되는데, 이를 js로 처리하면 이와 같다.

```vue
<template>
	<form ref="signupForm">
    ...
  </form>
</template>
<script setup>
  // templates ref
  const signupForm = ref(null);
    onMounted(async () => {
      await nextTick(() => {
        // HttpCollection을 Array화 후 forEach로 탐색
        Array.from(signupForm.value.children).forEach((tag) => {
          if (tag.tagName === 'INPUT') {
            tag.addEventListener('invalid', function (e) {
              // e.preventDefault();
              // invalid시 메세지출력이 이제 안되므로 특정 div에 클래스명 넣어 정보 입력 유도
            });
          }
        });
      });
    });
</script>
```

하지만 `templates ref`가 필수적이라는 단점과(Vue 공식문서에서는 ref의 사용을 되도록 회피하라고 안내하고 있다.) 더불어 아래와 같은 경우에는 INPUT을 하위 컴포넌트에서 찾는 과정을 한번 더 거쳐야 해서 매우 불편하다.

```vue
<template>
	<form>
    <label></label>
    <input required>
    <!-- input을 div 내에서 다시 검색해야 한다. -->
    <div>
      <label></label>
      <input required>
  	</div>
  </form>
</template>
```

그럴 때 사용할 수 있는 것이 v-on:invalid이다.

- invalid : input이 검증을 통과 못했을 경우 동작
- Prevent: prevent property는 `event.preventDefault()`와 같은 동작을 한다.

```vue
<template>
	<form>
    <label></label>
    <input @invalid.prevent>
  </form>
</template>
```



### Form의 새로고침 문제와 v-on:submit.prevent

위와 비슷한 문제로,`<form>` 태그의 경우 내부에서 submit시 기본 동작으로 새로고침을 실행하는데, 이를 방지하는 것이 `@submit.prevent=""`이다.

```vue
<template>
	<!-- submit시 동작하며, 함수를 연결해 기존 button 등에서 실행하던 함수를 여기서 적용할 수 있다. -->
	<!-- prevent는 기본 동작인 새로고침을 방지 -->
	<form @submit.prevent="">
    
  </form>
</template>
```



# 업데이트 기록

- 2022.10.05 : pinia / font awesome 사용방법 추가
- 2022.10.06 : Route Middleware

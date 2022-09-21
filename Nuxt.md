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
- index.vue는 홈페이지 역할을 한다.
- Vue.js는 실제로는 페이지가 하나이지만 브라우저의 히스토리 API를 이용해서 컴포넌트를 바꿔치기 하는 방식이었다면(url 변경으로 바꿔치기), Nuxt는 진짜로 pages 내의 컴포넌트가 하나의 개별적인 페이지이다.(코드 스플리팅)
- 즉, 코드 스플리팅으로 인해 SSR 방식이 적용되었으며 Vue.js의 CSR처럼 파일을 전부 불러오는게 아닌 페이지별로 불러오게 된다. CSR의 새 페이지 로딩시 페이지 깜빡임 등을 방지하기 위해 다음에 옮겨갈 페이지들을 미리 가져오는 기능도 적용되어 있다.

## Layouts

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
<!-- 반드시 layouts에 설정할 필요는 없고, 원하는 곳에 설정 가능 -->
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
  <title>index - Site Title</title>
</head>
```



## Nuxt.config.js(Nuxt.config.ts)

- 공통으로 사용하는 설정, 객체 등을 적용하는 데 이용한다.

```

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


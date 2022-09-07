# Nuxt.js

## 특징

- React의 next.js보다 DX(Developer experience)가 훨씬 좋다.
- 폴더를 통해 기능을 인식하기 때문에 폴더명을 정해진대로 입력해야 한다.
- Vue.js처럼 app.vue를 이용할 수도 있고, app.vue 없이 구성하면 `pages/index.vue`를 홈페이지로 삼거나, `layouts/default.vue`를 기본화면으로 삼는다.



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
    <NuxtPage />
  </div>
</template>
```

Custom layouts

```vue
<template>
  <div>
    {{ data }} page
  </div>
</template>
<script>
definePageMeta({
  layout: 'custom'
})
export default {
  setup() {
    const data = 'profile';
    return {
      data
    }
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
      return {
        dynamic: ref(77)
      }
    },
	}
</script>

<!-- console 내 결과 -->
<head>
  <title>77 - Site Title</title>
</head>
```



## Nuxt.config.js(Nuxt.config.ts)

- 공통으로 사용하는 설정, 객체 등을 적용하는 데 이용한다.

```

```


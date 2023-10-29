# Laravel + Vue3 + Vue-Router

## Install Vue3

オフィシャルのドキュメントではインストール方法が記載されていないため、viteのプラグインをインストールする

- https://github.com/vitejs/vite-plugin-vue/tree/main/packages/plugin-vue

```bash
$ npm install @vitejs/plugin-vue
```

vite.config.jsを開き、以下の記述を追加する

```js
# vite.config.js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import vue from '@vitejs/plugin-vue'; <--

export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: true,
        }),
		vue() <--
    ],
});
```

```
$ npm run dev
```

## Vue3がコンパイルされるかテストする

```vue
# resources/js/TestVueApp.vue
<script setup>
import { ref } from 'vue'

const count = ref(0)
function increment() {
  count.value++
}
</script>

<template>
  <!-- make this button work -->
  <button @click="increment">count is: {{ count }}</button>
</template>
```

```js
# resources/js/app.ts
import { createApp } from "vue/dist/vue.esm-bundler";
import TestVueApp from "./TestVueApp.vue";

createApp(TestVueApp).mount("#test-vue-app");
```

```php
# routes/web.php
Route::get('/', function () {
	return view('vue');
});
```

```php
# resources/view/vue.blade.php
<!DOCTYPE html>
<html>

<head>
	@vite(['resources/js/app.ts'])
</head>

<body>
	<div id="test-vue-app"></div>
</body>

</html>
```

## TypeScript対応

ビルドは出来るが、app.tsからxxx.vueを読み込む時にwarningが出る

型宣言ファイルを入れることで解決する

- https://miyauchi.dev/ja/posts/vite-vue3-typescript/


```ts
# resources/js/shims-vue.d.ts
declare module '*.vue' {
	import type { DefineComponent } from 'vue'
	const component: DefineComponent<Record<string,unknown>, Record<string,unknown>, unknown>
	export default component
}
```


## Vue-Routerの導入

https://router.vuejs.org/installation.html

```bash
$ npm install vue-router@4
```

router.tsの作成

```ts
# resources/js/router.ts
import { createRouter, createWebHashHistory } from 'vue-router'

const Home = { template: '<div>Home</div>' }
const About = { template: '<div>About</div>' }

const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About },
]

export const router = createRouter({
  history: createWebHashHistory(),
  routes,
})
```

App.vueの作成

```ts
<script lang="ts" setup>

</script>

<template>
	<h1>App</h1>
	
	<router-link to="/">Go to Home</router-link>
    <router-link to="/about">Go to About</router-link>

	<router-view></router-view>
</template>
```

app.tsの書き換え

```ts
import { createApp } from "vue/dist/vue.esm-bundler";
import App from "./App.vue";
import { router } from "./router";

const app = createApp(App)
app.use(router);
app.mount("#test-vue-app");
```

## Historyモードへの切り替え

createWebHashHistory()ではなく、createWebHistory()を使う

```ts
export const router = createRouter({
  //history: createWebHashHistory(),
  history: createWebHistory(),
  routes,
})
```

リロードするとエラーになるため、Laravel側も対応する

```php
# routes/web.php
Route::get('/', function () {
	return view('vue');
});

Route::fallback(function () {
	return view('vue');
});
```

### 何故fallbackを使うか

vueで取り扱うURLが１階層に限定される場合は``/{any}``でも対応可能だが、
``/dettail/123``のようなIDを組み込んだURLにする場合も多いため、
fallbackで

```php
Route::get('/{any}', function () {
	return view('vue');
});
//-> /detail/123というURLでは404 Not Foundになる
```
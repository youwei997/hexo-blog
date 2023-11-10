---
title: pinia使用
date: 2023-11-11 02:39:45
tags:
categories: vue
---
### 解构

使用storeToRefs，取值必须的使用 .value

```vue
<script setup>
import { defineComponent, ref } from 'vue';
import { storeToRefs } from 'pinia';
import { useUserStore } from '@/stores/user.js';

const userStore = useUserStore();
// 使用storeToRefs，取值必须的使用 .value
const { isLogin, userInfo } = storeToRefs(userStore);

const takein = () => {
	if (!isLogin.value) {
		uni.navigateTo({
			url: '/pages/login/login'
		});
	}

</script>
```
### 映射state和getter

#### useCounterStore

```js
// stores/counter.js
import {
	defineStore
} from 'pinia';

export const useCounterStore = defineStore('counter', {
	state: () => {
		return {
			count: 0
		};
	},
	// 也可以这样定义
	// state: () => ({ count: 0 })
	actions: {
		increment() {
			this.count++;
			console.log(this.count);
		},
	},
	getters: {
		doubleCount: (state) => state.count * 2,
	},
	unistorage: true // 开启后对 state 的数据读写都将持久化
});
```



#### 使用 script setup 

```vue
<script setup>
 const store = useStore()

 store.counter = 3
 store.doubleCount // 6
</script>
```



#### 使用defineComponent

```vue
<script>
import { defineComponent, ref } from 'vue';
import { mapState, mapGetters } from 'pinia';
import { useCounterStore } from '@/stores/counter';

export default defineComponent({
	computed: {
		...mapState(useCounterStore, ['count']),
		...mapGetters(useCounterStore, ['doubleCount'])
	}
});
</script>
```



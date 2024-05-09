### 1、安装pinia

### 2、创建一个 pinia（根存储）并将其传递给应用程序：

```
import { createPinia } from 'pinia'
app.use(createPinia())
```

### 3、定义一个store

```
import { defineStore } from 'pinia'
// useStore 可以是 useUser、useCart 之类的任何东西
export const useStore = defineStore({
	id: 'user',  //store 的唯一 id
  // other options...
  state: () => ({
  	counter: 0,
  	name: '',
    userId: ''
  }),
  getters: {
  // 只会依赖状态
  	doubleCount(state) {
      return this.counter * 2
    }
  },
  actions: {
  	// 可异步
  	async registerUser() {}
  }
})
```

### 4、使用store

```
// 在setup()中调用 useStore() 之前不会创建 store
<script setup>
	import { useStore } from '@/store'
	const store = useStore()
	// store 是一个用reactive对象，不能直接对其进行结构（会破坏响应式，同props）
	// 可利用 storeToRefs() 保持响应式
	const { name, userId } = storeToRefs(store)
	
	// 一般可直接读取和写入状态(包含getter)
	store.counter++
	console.log(store.doubleCount)
	
	// 重置状态
	store.$reset()
	
	// 替换state
	store.$state = { counter: 666, name: 'Paimon' }
	
	// 调用actions方法
	store.registerUser()
</script>
```

### 5、状态持久化到本地

1) 通过plugin配置

```
// 安装 pinia-plugin-persistedstate
import piniaPluginPersistedstate from 'pinia-plugin-persistedstate'

...

export const store = createPinia()
store.use(piniaPluginPersistedstate)

export const useStore = defineStore({
	id: 'user',  //store 的唯一 id
	persist: {
		storage： window.localStorage
	} // 开启数据持久化
})
```

2) 通过 store 的 `$subscribe()` 方法查看状态及其变化：

```
// 此订阅将在组件卸载后保留
someStore.$subscribe((mutation, state) => {
	localStorage.setItem('store', JSON.stringify(state))
}, { detached: true })
```

3) 可在pinia实例上查看整个状态

```
watch(
	pinia.state,
	(state) => {
		localStorage.setItem('store', JSON.stringify(state))
	},
	{ deep: true }
)
```

### 7、plugin插件

作用：扩展store

- 向 Store 添加新属性
- 定义 Store 时添加新选项
- 为 Store 添加新方法
- 包装现有方法
- 更改甚至取消操作
- 实现**本地存储**等副作用
- **仅**适用于特定 Store

示例：5、2)

### 8、模块化

```
// store/index.js
import useUserStore from './user'
import useCounterStore from './counter'

// 统一导出useStore方法
export default function useStore() {
  return {
    user: useUserStore(),
    counter: useCounterStore(),
  }
}
```


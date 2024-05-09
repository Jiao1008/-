1、安装 vue-router

2、创建router示例

```
// router/index.js
import { createRouter, createWebHashHistory } from 'vue-router';

// 定义路由
const routes = [
  { path: '/', component: () => import() },
  { path: '/about', () => import() },
]
// 创建路由实例
const router = createRouter({
	history: createWebHashHistory(), // hash模式
  routes,
  scrollBehavior: () => {}
})

export default router
```

3、引入router实例

```
import router from './router'

// 将路由实例挂载到根实例
app.use(router)
```

4、组件访问

```
// setup
const route = useRoute()
const router = useRouter()
this.$route.params

this.$router.push(url)
```


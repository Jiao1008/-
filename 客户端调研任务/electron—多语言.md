### 1. 多语言相关配置

1）安装vue-i18n和unplugin-vue-i18n插件，并实现部署后更新多语言

```
// main.js
import { createI18n } from 'vue-i18n'
import messages from '@intlify/unplugin-vue-i18n/messages'

const i18n = createI18n({
  locale: 'en',
  messages,
})
app.use(i18n)
```

新建locales语言包文件夹，在其下新建 zh.js , en.js 等语言包文件

在 electron.vite.config.js 中配置

   ```
   import VueI18nPlugin from '@intlify/unplugin-vue-i18n/vite'
   
   export default defineConfig(() => {
   	return {
   		renderer: {
   			VueI18nPlugin({
             		include: [resolve(__dirname, './src/renderer/src/locales/**')],
             		jitCompilation: true,
            		dropMessageCompiler: true,
           }),
   		}
   	}
   })
   ```

### 3. 语言切换

   **1）渲染进程发送消息切换语言**
   ```text
   import { setLocale } from '@/i18n'
   // 通知主进程语言改变
   ipcRenderer.send("langChange", lang);
   ```

   **2）监听语言变更消息**
   ```
   // 主进程
   const vueI18n = new Vue({ i18n });
   ipcMain.on("langChange", (event, lang) => {
   	// 更换托盘等系统语言
   	vueI18n.global.locale = lang
   	// 更换窗口页面语言
   	for (const currentWin of BrowserWindow.getAllWindows()) {
       const webContentsId = currentWin.webContents.id;
       // 过滤出发送通知的窗口
       if (webContentsId !== event.sender.id && !currentWin.isDestroyed) {
         currentWin.webContents.send('langChange', lang)
       }
     }
   }
   // 渲染进程
   ipcRenderer.on('langChange', (event, args) => {
   	// 语言包更换
   	setLocalLang(lang)
   	// 处理一些更语言相关的其他逻辑
   } )
   
   setLocalLang(lang){
   	$i18n.locale = lang
   }
   ```

   渲染进程语言切换也可通过BUS.$emit()、BUS.$on()实现

### 4. 使用
   ```
   // 主进程
   vueI18n.global.$t('xxx')
   // 渲染进程 
   $t('xxx')
   // 外部js使用，先导入语言配置文件 
   import i8n from './locals/index.js'
   使用 i18n.global.$t('xxx')
   ```

### 5. 根据多语言实现界面css宽高的定制
   设置多语言样式在body来定制 class="lang-zh/lang-en_US"
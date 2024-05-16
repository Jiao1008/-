# @layer

```
/* 隐含级联层 */
h1 {
    color: #09f;
}

/* 首个匿名级联层 */
@layer {
    h1 {
        color: yellow;
    }
}

/* 预设级联层顺序 */
@layer base, theme;

/* 给base级联层添加样式 */
@layer base {
    h1 {
        color: orange;
    }
}

/* 给 theme 级联层添加样式 */
@layer theme {
    h1 {
        color: lime;
    }
}

/* 新增匿名层，将会合进首个已创建的匿名级联层 */
@layer {
    h1 {
        color: #f36;
    }
}
```

这个示例的级联层顺序是：<br/>

- Anonymous Layer (匿名级联层)<br/>

- base<br/>

- theme<br/>

- Anonymous Layer (匿名级联层)<br/>

- Unlayer（隐含级联层）<br/>



​如果**@layer {}**创建的匿名级联层出现在 @layer单行语法预设级联层之前，则该匿名级联层会放置在已预设的级联层最前面，匿名级联层优先级最低；如果@layer{}创建的匿名级联层出现在@layer单行语法预设的级联层之后，则该匿名级联层会追加到已预设的级联层末尾（最后面），匿名级联层优先级最高；如果@layer{}同时出现在@layer单行语法预设的级联层前后，则会按照前面两种情况对匿名级联层排序，最终排在后面的匿名级联层获胜。对于隐含级联层而言，它始终会在所有级联层最后，具有最高的优先级。

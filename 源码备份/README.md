# 1、样式修改
目录:E:\project\vuepressBlog\node_modules\vuepress-theme-reco\components\Navbar.vue
修改了以上三个文件
源码版本："vuepress-theme-reco": "^1.6.1"


# 2、修复中文跳转问题
目录： node_modules/vue-router/dist/vue-router.esm.js
```js
function match (
    raw,
    currentRoute,
    redirectedFrom
  ) {

  if(typeof raw ==='string'){
     raw = decode(raw)
  }
  // ...code
}
```

## 3、修复全局查询问题
**fixed**  
- 3-1 删除主题变换按钮
- 目录`docs/.vuepress/theme/components/Mode/index.vue`
   注释`class="color-button"`的元素
- 3-2 修复查询问题
  目录`node_modules/@vuepress/plugin-search/match-query.js`
  ```js
    if (get(page, 'frontmatter.tags')) {
    if(Array.isArray(page.frontmatter.tags)){
    domain += ` ${page.frontmatter.tags.join(' ')}`
    }
    }
  ```

# 使用 Vue 3.0 和 Canvas 实现哔哩哔哩首页 Banner 特效

摸鱼看哔哩哔哩发现 banner 换了, 22的眼睛还会一眨一眨的, 鼠标移上去会有景深和位移的变化.  
有丶意思, 我按 F12 观察了一波, 发现是用几张图片配合CSS实现的. 正好之前 Vue3.0 发布了, 想搞个 demo 写一写, 于是就用 vite 简单搭了一个项目.
使用 Vue 3.0 的 Composition API 和 Canvas 实现了哔哩哔哩的这个 banner 效果. 具体效果可以点击[这个网址](https://liuycy.github.io/bilibili-autumn/)预览.

## 本地运行
毕竟 github pages 的访问速度有点慢, 所以你如果有兴趣的话, 也可以将本仓库 clone 到本地运行查看效果

```bash
# git clone 到本地后, cd 到仓库文件夹执行
npm install
# 然后运行
npm run dev
```

然后打开浏览器访问 http://localhost:3000/ 就能看到效果了

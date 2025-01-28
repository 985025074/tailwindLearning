# 2025 年 1 月 17 日 13 点 02 分

# 配置：

```shell
npm create vite@latest tailwindInstall -- --template react
cd tailwindInstall
```

```shell
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

注：-p 的作用是 生成 postcss 配置文件
# 导入CSS 文件:
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

```
# 官方教程的预设

```js
/** @type {import('tailwindcss').Config} */
export default {
  content: ["./index.html", "./src/**/*.{js,ts,jsx,tsx}"],
  theme: {
    extend: {},
  },
  plugins: [],
};
```

然后就可以使用
# prettier:
https://github.com/tailwindlabs/prettier-plugin-tailwindcss
# 问题：vscode @tailwind 报错 unknow rules:

安装 postCSS 插件
https://github.com/tailwindlabs/tailwindcss/discussions/13881

# 条件式:

hover:+类名
常见的有

- Pseudo-classes, like :hover, :focus, :first-child, and :required
- Pseudo-elements, like ::before, ::after, ::placeholder, and ::selection
- Media and feature queries, like responsive breakpoints, dark mode, and prefers-reduced-motion
- Attribute selectors, like [dir="rtl"] and [open]

## 按钮设计为例：

```html
<button
  class="bg-violet-500 hover:bg-violet-600 active:bg-violet-700 focus:outline-none focus:ring focus:ring-violet-300 ..."
>
  Save changes
</button>
```

active：按下到 释放的这段时间
focus：直接就是点击后的聚焦状态

## 表格设计

first last 的使用示例（：first-child :last child）
类似的有 odd： even:

```js
<ul role="list" class="p-6 divide-y divide-slate-200">
  {#each people as person}
    <!-- Remove top/bottom padding when first/last child -->
    <li class="flex py-4 first:pt-0 last:pb-0">
      <img class="h-10 w-10 rounded-full" src="{person.imageUrl}" alt="" />
      <div class="ml-3 overflow-hidden">
        <p class="text-sm font-medium text-slate-900">{person.name}</p>
        <p class="text-sm text-slate-500 truncate">{person.email}</p>
      </div>
    </li>
  {/each}
</ul>
```

## input 设计：

```html
<form>
  <label class="block">
    <span class="block text-sm font-medium text-slate-700">Username</span>
    <!-- Using form state modifiers, the classes can be identical for every input -->
    <input
      type="text"
      value="tbone"
      disabled
      class="mt-1 block w-full px-3 py-2 bg-white border border-slate-300 rounded-md text-sm shadow-sm placeholder-slate-400
      focus:outline-none focus:border-sky-500 focus:ring-1 focus:ring-sky-500
      disabled:bg-slate-50 disabled:text-slate-500 disabled:border-slate-200 disabled:shadow-none
      invalid:border-pink-500 invalid:text-pink-600
      focus:invalid:border-pink-500 focus:invalid:ring-pink-500
    "
    />
  </label>
  <!-- ... -->
</form>
```

## 子元素背景的联动 group
父元素用group 子元素用group-xxx: xxx是动作
可以使用group-xxx动作/name:实现更进一步的细粒度控制
```html
<a
  href="#"
  class="group block max-w-xs mx-auto rounded-lg p-6 bg-white ring-1 ring-slate-900/5 shadow-lg space-y-3 hover:bg-sky-500 hover:ring-sky-500"
>
  <div class="flex items-center space-x-3">
    <svg
      class="h-6 w-6 stroke-sky-500 group-hover:stroke-white"
      fill="none"
      viewBox="0 0 24 24"
    >
      <!-- ... -->
    </svg>
    <h3 class="text-slate-900 group-hover:text-white text-sm font-semibold">
      New project
    </h3>
  </div>
  <p class="text-slate-500 group-hover:text-white text-sm">
    Create a new project from a variety of starting templates.
  </p>
</a>
```

# 自定义动作
当published 成立时候才显示
```js
<div class="group is-published">
  <div class="hidden group-[.is-published]:block">
    Published
  </div>
</div>
```
解释_ :代表空格 
解释& 代表当前class（在group中含义是父的group 正式）
测试得_ 必选，在这种语境下，另外 &含义代表得是.group

```html
<div class="group">
  <div class="group-[:nth-of-type(3)_&]:block">
    <!-- ... -->
  </div>
</div>

```
# 奇怪的逻辑解释：
‘只有前两个量，原因是 
展开的CSS是
.nth .group  名称
第一个能亮是因为 root 符合.nth
第二个也是因为某种原因....

```html
import "./App.css";

export default function App() {
  return (
    <div className="father">
      <div className="group">
        <div className="group-[:nth-child(1)_&]:bg-red-600">你好</div>
      </div>
      <div className="group">
        <div className="group-[:nth-child(2)_&]:bg-red-600">你好</div>
      </div>
      <div className="group">
        <div className="group-[:nth-child(3)_&]:bg-red-600">你好</div>
      </div>
      <div className="group">
        <div className="group-[:nth-child(4)_&]:bg-red-600">你好</div>
      </div>
      <div className="group">
        <div className="group-[:nth-child(5)_&]:bg-red-600">你好</div>
      </div>
      <div className="group">
        <div className="group-[:nth-child(6)_&]:bg-red-600">你好</div>
      </div>
    </div>
  );
}

```
再重申
有[]和没有[_&] 区别是选择器附加位置。
没有[] 那就是[xx] => .groupxx
如果有[xxx_&]那就是xxx .group
如果你想单独拿出来[]:不跟group 必须要要带上_&否则非法。
# 基于子兄弟的状态peer 示例，适时的展示错误信息
只支持前面的兄弟使用
```js
<form>
  <label class="block">
    <span class="block text-sm font-medium text-slate-700">Email</span>
    <input type="email" class="peer ..."/>
    <p class="mt-2 invisible peer-invalid:visible text-pink-600 text-sm">
      Please provide a valid email address.
    </p>
  </label>
</form>

```
https://tailwindcss.com/docs/hover-focus-and-other-states#
# 控制直接子级元素样式 *样式
示例：categories 橱窗.
注意 以这种方式子级无法被重写样式
```html
<div>
  <h2>Categories<h2>
  <ul class="*:rounded-full *:border *:border-sky-100 *:bg-sky-50 *:px-2 *:py-0.5 dark:text-sky-300 dark:*:border-sky-500/15 dark:*:bg-sky-500/10 ...">
    <li>Sales</li>
    <li>Marketing</li>
    <li>SEO</li>
    <!-- ... -->
  </ul>
</div>
```
# 子样式 has：
```js
<label class="has-[:checked]:bg-indigo-50 has-[:checked]:text-indigo-900 has-[:checked]:ring-indigo-200 ..">
  <svg fill="currentColor">
    <!-- ... -->
  </svg>
  Google Pay
  <input type="radio" class="checked:border-indigo-500 ..." />
</label>
```
## has 支持组合group-has 和peer-has
## 注意顺序对于部分文档是重要的。

# 状态符号文档：
。。。其余参考文档：包括自定义
https://tailwindcss.com/docs/hover-focus-and-other-states#using-arbitrary-variants

# 响应式设计
https://tailwindcss.com/docs/responsive-design
Breakpoint prefix	Minimum width	CSS
sm	640px	@media (min-width: 640px) { ... }
md	768px	@media (min-width: 768px) { ... }
lg	1024px	@media (min-width: 1024px) { ... }
xl	1280px	@media (min-width: 1280px) { ... }
2xl	1536px	@media (min-width: 1536px) { ... }
默认是mobile -first
# 暗色模式
dark:
默认跟系统的来，除非你指定：
https://tailwindcss.com/docs/dark-mode
# 多光标编辑：
https://code.visualstudio.com/docs/editor/codebasics#_multiple-selections-multicursor
# 自定义值
top-[值]
top-[--变量]
## 自定义CSS 变量:"
<div class="grid grid-cols-[fit-content(theme(spacing.32))]">
  <!-- ... -->
</div>
```

## 多个CSS 可能有问题:
https://tailwindcss.com/docs/adding-custom-styles
# 复用CSS/自定义
@layer 层级{
  名称{
    @apply 可选,这后面可以跟tailwind格式
  }
}

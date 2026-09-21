# 动画角色登录页面 (Animated Login Page)

一个带有交互动画角色的 React 登录页面，采用 Vite 构建。

## 🎯 特性

- **交互动画角色**：左侧面板展示了四个可爱的动画角色（紫色、黑色、橙色、黄色），它们会响应用户的输入行为
- **智能眼睛跟随**：角色的眼睛会跟随鼠标移动，仿佛在观察你的操作
- **输入状态反馈**：
  - 输入邮箱时，紫色和黑色会互相对视
  - 输入密码时，角色会做出害羞反应（遮眼/转头），黑色还会偷偷往回瞟
  - 密码点开明文时，四个都凑过去看
  - 登录失败时摇头、垮脸（黑色垂眼、紫色橙色撇嘴、黄色波浪嘴）
  - 登录成功时一起咧嘴
- **表情细节**：紫色和橙色有会变形的嘴，黄色是一条会弯的线；随机眨眼
- **视觉设计**：
  - 渐变背景配合柔和的模糊光效
  - 现代化表单设计，支持显示/隐藏密码
  - 按钮悬停动画效果
  - 响应式布局，移动端自动隐藏动画角色

## 🛠️ 技术栈

- React 18
- Vite
- Tailwind CSS
- CSS 动画

## 📦 安装与运行

```bash
# 安装依赖
npm install

# 启动开发服务器
npm run dev
```

访问本地地址（通常为 `http://localhost:5173`）。

## 📁 项目结构

```
src/
├── App.jsx                 # 主页面组件，包含登录表单逻辑
├── App.css                 # 全局样式
├── main.jsx                # React 入口
└── components/
    └── animated-characters/
        ├── AnimatedCharacters.jsx  # 动画角色核心组件
        └── index.js
```

## 🔌 复用动画组件

动画角色组件是独立可复用的，可以集成到其他项目中：

```jsx
import { AnimatedCharacters } from "./components";

<AnimatedCharacters
  isTyping={isTyping}
  isPasswordFocused={isPasswordFocused}
  showPassword={showPassword}
  passwordLength={password.length}
  loginFailed={loginFailed}
  loginSuccess={loginSuccess}
/>
```

组件自带样式（`AnimatedCharacters.css`，自己 import），整个
`animated-characters` 目录拷走就能用，不依赖宿主的 App.css。

### Props 说明

| Prop | 类型 | 说明 |
|------|------|------|
| `isTyping` | boolean | 邮箱输入框是否获得焦点 |
| `isPasswordFocused` | boolean | 密码输入框是否获得焦点 |
| `showPassword` | boolean | 密码是否可见 |
| `passwordLength` | number | 密码长度 |
| `loginFailed` | boolean | 登录失败 → 摇头 + 垮脸。要重播动画就 false→true 翻一次 |
| `loginSuccess` | boolean | 登录成功 → 咧嘴 |

### 姿态优先级

同一时刻只会命中一种，从高到低：

1. **偷看** — 密码已填且明文：四个都凑过去看，紫色每隔 2–5 秒再瞄一眼
2. **回避** — 密码框聚焦且密文：集体转头，紫色左倾 14°、黑色右倾 12°；
   黑色每隔 2–5 秒往右瞟一眼密码框（捂着眼从指缝里偷看那个意思）
3. **对视** — 邮箱框聚焦后的头 0.8 秒：紫色和黑色互看
4. **默认** — 眼睛和身体跟随鼠标

## 🎨 表单验证规则

- **邮箱**：必须为有效的邮箱格式
- **密码**：至少 6 个字符
- 错误信息会显示在表单下方

演示用：邮箱填 `demo@example.com` 走成功分支（咧嘴），其他组合演示失败（摇头）。

## 🐛 已修复的问题

- **紫色的眼睛会被裁掉**：场景容器原本 400px 高且 `overflow: hidden`，
  但紫色在「输入 / 回避」状态会长到 440px，顶部溢出 40px——眼睛正好在这一段里。
  现在容器 450px，多出来的是透明余量，角色仍然贴底对齐，构图没变。
- **摇头从来没生效过**：`App.css` 里有 `shakeHead` 关键帧和 `.shake-head` 类，
  但 JSX 从未引用，README 描述的「提交时摇头」是空的。现在接到了 `loginFailed`。
  重播动画用的是两个名字不同、内容相同的关键帧交替——光把 class 摘掉再挂上，
  浏览器会把同一帧里的增删合并，动画不会重新开始。
- **mousemove 直接 setState**：一秒几百次事件全都触发 React 重渲染。
  现在用 requestAnimationFrame 节流，一帧最多算一次。
- **紫色和黑色之间露缝**：紫色收窄到 150px 后必须同时右移到 `left: 110`，
  让它和黑色（`left: 240`）保持 20px 重叠，否则身体一倾斜就能看见背景。
- 新增 `prefers-reduced-motion` 支持：用户在系统里关了动效就只保留静态姿势。

## 📝 License

MIT
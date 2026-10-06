# 爱问答助手 (AiAskHelper) - 使用与配置说明书

《爱问答助手》是一款功能强大、多平台兼容的网课自动答题 UserScript（油猴脚本）。脚本内嵌了丰富的题库接口、本地题库缓存机制，并**深度集成 AI 智能辅助答题**（如：智谱清言、讯飞星火、豆包等）。

---

## 🌟 核心功能特性
1. **多平台适配支持**：支持超星、知到智慧树、国家开放大学、广东开放大学等三十多个常见网课平台的随堂练习、作业及考试页面。
2. **自动化答题**：支持自动获取题目、匹配题库、自动填入答案，并支持自动提交和题目自动翻页。
3. **本地缓存题库管理**：不仅通过云端请求答案，还会将做过的题目缓存至本地，实现离线题库秒出答案，避免过多调用远端 API。
4. **悬浮窗与面板**：提供现代化的悬浮面板（包含答题面板、日志系统、接口调试、AI解答等）。
5. **✨ AI 智能搜索与答题**：在官方题库或免费接口无答案时，可调用设定好的大语言模型（LLM）实现兜底解答。

---

## 🤖 重点关注：AI 智能答题模块

脚本内置了连接大语言模型的逻辑（封装在 `aiAsk` 和相关的 `ApiAnswerMatch` 方法内）。当开启 **“AI 辅助答题”** 开关时，脚本会提取网页上的 `题型`、`题干` 和 `选项`，按照预设的系统 Prompt 组装后发送给配置好的 API。

### 🔄 更换 / 配置 API 代码位置

默认的 AI 模型配置存放在脚本定义的一个叫做 `Pt` 的全局配置对象内（位于 **第 8296 行** 附近）。

在代码中搜索 `gpt: [` 或 `desc: "智谱清言4.0"`，你会找到类似下面的配置数组：

```javascript
const Pt = {
    // ...其他基础配置
    gpt: [ 
        {
            name: "GLM",
            desc: "智谱清言4.0",
            api: "http://82.157.105.20:8002/v1/chat/completions",
            key: "",  // 在这里填入你个人的 API KEY
            msg: "AI响应异常，可能是没有获取cookie...",
            home: "https://chatglm.cn/main/alltoolsdetail",
            recommend: 3,
            model: "gpt-4o" // 调用时传入的模型名称
        }, 
        {
            name: "spark",
            desc: "讯飞星火",
            api: "http://82.157.105.20:8000/v1/chat/completions",
            key: "",  // 在这里填入你个人的 API KEY
            model: "gpt-4o"
            // ...
        }, 
        {
            name: "doubao",
            desc: "豆包",
            api: "https://ark.cn-beijing.volces.com/api/v3/chat/completions",
            key: "ark-xxxx-xxxx-xxxx", // 在这里更换为新的火山引擎 API KEY
            msg: "AI响应异常，请检查豆包API Key是否正确配置",
            home: "https://console.volcengine.com/ark/region:ark+cn-beijing/endpoint",
            recommend: 4,
            model: "ep-20260613181604-l2pp6" // 根据你的豆包接入点更改接入点名称
        } 
    ],
    gptIndex: 2, // 默认选中的是数组里的第 3 项（即“豆包”）
    askGpt: !1,  // 默认是否开启 AI 辅助答题功能 (false)
    hotkey: "Ctrl+Shift+H",
    // ...
};
```

### 🛠 如何添加或替换为你自己的 API 接口（如 OpenAI / DeepSeek / 通义千问）？

如果想改为兼容 OpenAI 格式的其他大模型接口（例如使用官方 GPT-4o 或 DeepSeek 等），只需在上面的 `gpt` 数组中添加或修改一个对象项：

```javascript
        {
            name: "deepseek",
            desc: "DeepSeek大模型",
            api: "https://api.deepseek.com/chat/completions", // 替换为你使用的第三方 API 地址
            key: "sk-abcdefg1234567890", // 替换为你的私有 API KEY
            msg: "AI响应异常，请检查API Key是否过期或网络是否畅通",
            home: "https://platform.deepseek.com/",
            recommend: 5,
            model: "deepseek-chat" // 填写实际使用的模型名称
        }
```

#### 注意事项：
1. `api` 地址必须接受标准的 OpenAI `/v1/chat/completions` 请求格式。
2. 配置更新后，可以在脚本的图形界面（悬浮窗 -> “⚙️系统设置” -> “🤖AI 设置”）的下拉菜单中看到并切换生效对应的模型。
3. 脚本会自动为请求加上 `Authorization: Bearer <你的Key>` 的请求头，因此直接将密钥填入 `key` 字段即可。

---

## 🖱 基础使用说明
- **呼出悬浮窗**：按快捷键 `Ctrl + Shift + H`，或点击右下角的圆形图标。
- **自动答题**：在悬浮窗的“答题界面”中开启“自动答题”、“自动跳转”开关。进入支持的考试/作业页面后点击【开始答题】即可。
- **AI 搜题**：悬浮窗左侧菜单选择【AI搜题】，可以在输入框中粘贴文字，直接让配置好的模型返回精确解答。

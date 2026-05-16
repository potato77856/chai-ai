# ChaiAPI 接口文档完整整理

> **整理时间**: 2026-05-15  
> **数据来源**: https://chaiapi.apifox.cn/  
> **Base URL**: `https://chaiapi.com`  
> **接口总数**: 33个

---

## 目录

- [一、通用说明](#一通用说明)
- [二、OpenAI 接口](#二openai-接口)
  - [2.1 聊天接口 (Chat Completions)](#21-聊天接口-chat-completions)
  - [2.2 Codex Responses API](#22-codex-responses-api)
  - [2.3 图像生成 (Image Generations)](#23-图像生成-image-generations)
  - [2.4 图像编辑 (Image Edits)](#24-图像编辑-image-edits)
  - [2.5 嵌入向量 (Embeddings)](#25-嵌入向量-embeddings)
  - [2.6 图生图（对话格式）](#26-图生图对话格式)
  - [2.7 文本转语音 (TTS)](#27-文本转语音-tts)
  - [2.8 语音转文本 (STT)](#28-语音转文本-stt)
- [三、Gemini 接口](#三gemini-接口)
  - [3.1 对话格式 (Generate Content)](#31-对话格式-generate-content)
  - [3.2 Gemini 生图](#32-gemini-生图)
  - [3.3 Gemini 改图](#33-gemini-改图)
- [四、Anthropic 接口](#四anthropic-接口)
  - [4.1 Messages API](#41-messages-api)
- [五、图片生成](#五图片生成)
  - [5.1 豆包/即梦出图 - Chat格式](#51-豆包即梦出图---chat格式)
  - [5.2 豆包/即梦出图 - Image格式（普通生图）](#52-豆包即梦出图---image格式普通生图)
  - [5.3 豆包/即梦出图 - Image格式（高质量生图）](#53-豆包即梦出图---image格式高质量生图)
  - [5.4 豆包/即梦出图 - Image格式（图片编辑）](#54-豆包即梦出图---image格式图片编辑)
  - [5.5 gpt-image-2 生成图片 1K](#55-gpt-image-2-生成图片-1k)
  - [5.6 gpt-image-2 生成图片 124K](#56-gpt-image-2-生成图片-124k)
  - [5.7 gpt-image-2 编辑图片/图生图](#57-gpt-image-2-编辑图片图生图)
- [六、视频生成](#六视频生成)
  - [6.1 Veo3 异步视频生成](#61-veo3-异步视频生成)
  - [6.2 Sora-2 创建角色](#62-sora-2-创建角色)
  - [6.3 Sora-2 获取视频任务状态](#63-sora-2-获取视频任务状态)
  - [6.4 Sora-2 获取视频内容](#64-sora-2-获取视频内容)
  - [6.5 Sora-2 编辑视频 (Remix)](#65-sora-2-编辑视频-remix)
  - [6.6 Sora-2 创建视频（带Character）](#66-sora-2-创建视频带character)
  - [6.7 Sora-2 从任务中创建角色](#67-sora-2-从任务中创建角色)
  - [6.8 Sora-2 故事板创建视频](#68-sora-2-故事板创建视频)
  - [6.9 Grok 视频生成](#69-grok-视频生成)
  - [6.10 Seedance 2.0 异步视频生成](#610-seedance-20-异步视频生成)
  - [6.11 Seedance 2.0 任务查询进度](#611-seedance-20-任务查询进度)
  - [6.12 通用 - 任务查询结果](#612-通用---任务查询结果)
  - [6.13 通用 - 任务查询进度](#613-通用---任务查询进度)
  - [6.14 通用 - 异步视频生成](#614-通用---异步视频生成)

---

## 一、通用说明

### 认证方式
大部分接口使用 **API Key** 认证，通过 HTTP Header 传递：
```
Authorization: Bearer <API_KEY>
```

部分接口使用自定义 Header（如豆包使用 `Authorization: <doubao-draw分组key>`），部分接口未定义安全方案（开发中）。

### 通用错误码
| HTTP 状态码 | 说明 |
|:-----------:|------|
| 200 | 成功 |
| 400 | 请求参数错误 |
| 401 | API Key 错误或未授权 |
| 404 | 模型不存在或接口地址错误 |
| 429 | 额度不足或限速 |
| 500 | 服务器内部错误 |

### 通用 Base URL
```
https://chaiapi.com
```

---

## 二、OpenAI 接口

### 2.1 聊天接口 (Chat Completions)

**用途**: Cherry Studio OpenAI 兼容聊天接口。Cherry Studio 使用 OpenAI 接入模式时调用。

```
POST /v1/chat/completions
```

#### 请求头

| 参数名 | 类型 | 必填 | 示例值 |
|--------|------|:----:|--------|
| Content-Type | string | 是 | `application/json` |

#### 请求参数（核心，支持更多 OpenAI 标准参数扩展）

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|:----:|------|
| `model` | string | **是** | 模型名称，如 `gpt-4o-mini` |
| `messages` | array[object] | **是** | 对话消息数组 |
| `stream` | boolean | 否 | 是否启用流式输出，默认 `false` |

> **扩展参数**：支持 `max_tokens`、`tools`、`tool_choice`、`logprobs`、`top_logprobs` 等 OpenAI 标准参数。`content` 支持字符串（简单文本）和数组（多模态）两种形式。

**messages 对象**：

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `role` | string | **是** | `system` / `user` / `assistant` |
| `content` | string / array | **是** | 消息内容 |

#### 请求示例

**基本文本对话**：
```json
{
  "model": "gpt-4o-mini",
  "messages": [
    { "role": "user", "content": "你好" }
  ],
  "stream": false
}
```

**图像分析（多模态）**：
```json
{
  "model": "gpt-4.1",
  "messages": [
    {
      "role": "user",
      "content": [
        { "type": "text", "text": "这张图片里有什么？" },
        {
          "type": "image_url",
          "image_url": {
            "url": "https://upload.wikimedia.org/..."
          }
        }
      ]
    }
  ],
  "max_tokens": 300
}
```

**流式响应**：
```json
{
  "model": "gpt-4.1",
  "messages": [
    { "role": "user", "content": "你好！" }
  ],
  "stream": true
}
```

**函数调用**：
```json
{
  "model": "gpt-4.1",
  "messages": [
    { "role": "user", "content": "波士顿今天的天气怎么样？" }
  ],
  "tools": [
    {
      "type": "function",
      "function": {
        "name": "get_current_weather",
        "description": "获取指定位置的当前天气",
        "parameters": {
          "type": "object",
          "properties": {
            "location": { "type": "string", "description": "城市和州，例如 San Francisco, CA" },
            "unit": { "type": "string", "enum": ["celsius", "fahrenheit"] }
          },
          "required": ["location"]
        }
      }
    }
  ],
  "tool_choice": "auto"
}
```

**Logprobs**：
```json
{
  "model": "gpt-4.1",
  "messages": [{ "role": "user", "content": "你好！" }],
  "logprobs": true,
  "top_logprobs": 2
}
```

**视频输入（多模态）**：
```json
{
  "model": "qwen-vl-plus-2025-08-15",
  "messages": [
    {
      "role": "system",
      "content": [
        { "type": "text", "text": "You are a helpful assistant." }
      ]
    },
    {
      "role": "user",
      "content": [
        { "type": "video_url", "video_url": { "url": "https://help-static-aliyun-doc.aliyuncs.com/...mp4" } },
        { "type": "text", "text": "这段视频的内容是什么?" }
      ]
    }
  ]
}
```

#### 成功响应 (200)

```json
{
  "id": "chatcmpl_xxx",
  "object": "chat.completion",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "你好，有什么可以帮你？"
      },
      "finish_reason": "stop"
    }
  ]
}
```

| 响应字段 | 类型 | 说明 |
|----------|------|------|
| `id` | string | 本次完成的唯一标识 |
| `object` | string | 固定为 `chat.completion` |
| `choices` | array | 候选回答列表 |
| `choices[].index` | integer | 候选序号 |
| `choices[].message.role` | string | 固定为 `assistant` |
| `choices[].message.content` | string | 助手回复文本 |
| `choices[].finish_reason` | string | 结束原因（如 `stop`） |

#### 错误码

| HTTP 状态码 | 说明 |
|:-----------:|------|
| 401 | 令牌无效 |
| 404 | 模型不存在或接口地址错误 |
| 429 | 额度不足或限速 |

---

### 2.2 Codex Responses API

**用途**: Codex CLI 使用的 OpenAI Responses API 兼容中转接口。

```
POST /v1/responses
```

#### 请求头

| 参数名 | 类型 | 必填 | 示例值 |
|--------|------|:----:|--------|
| Content-Type | string | 否 | `application/json` |

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|:----:|------|
| `model` | string | **是** | 模型名称，如 `gpt-5-codex`、`gpt-4.1`、`o4-mini` |
| `input` | string/array | **是** | 用户输入，纯文本字符串或多模态消息数组 |
| `reasoning` | object | 否 | 推理配置对象 |
| `reasoning.effort` | string(枚举) | 否 | `low` / `medium` / `high` |
| `reasoning.summary` | string | 否 | 如 `detailed` |
| `tools` | array | 否 | 工具列表，支持 `web_search_preview` 和 `function` |
| `tool_choice` | string | 否 | 工具选择策略，如 `auto` |
| `instructions` | string | 否 | 系统级指令 |
| `stream` | boolean | 否 | 是否流式输出 |

#### 请求示例

**基础文本**：
```json
{
  "model": "gpt-5-codex",
  "input": "请帮我检查这个项目代码",
  "reasoning": { "effort": "medium" }
}
```

**网络搜索**：
```json
{
  "model": "gpt-4.1",
  "tools": [{ "type": "web_search_preview" }],
  "input": "今天有什么积极正面的新闻?"
}
```

**推理模式**：
```json
{
  "model": "o4-mini",
  "input": "一只啄木鸟能啄多少木头?",
  "reasoning": { "effort": "high", "summary": "detailed" }
}
```

#### 成功响应 (200)

```json
{
  "id": "resp_xxx",
  "object": "response",
  "model": "gpt-5-codex",
  "output_text": "这是模型返回内容"
}
```

| 响应字段 | 类型 | 说明 |
|----------|------|------|
| `id` | string | 响应唯一标识，如 `resp_xxx` |
| `object` | string | 固定为 `response` |
| `model` | string | 实际使用的模型名称 |
| `output_text` | string | 模型返回文本内容 |

#### 错误码

| HTTP 状态码 | 说明 |
|:-----------:|------|
| 401 | API Key 错误或未授权 |
| 429 | 额度不足或请求过于频繁 |
| 500 | 中转服务或上游模型异常 |

---

### 2.3 图像生成 (Image Generations)

**用途**: 给定文本提示和/或输入图片，模型将生成新的图片。支持 DALL·E 2、DALL·E 3 以及 GPT-Image-1 模型。

```
POST /v1/images/generations
```

#### 请求参数

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|:----:|--------|------|
| `prompt` | string | **是** | - | 生成图片的文本描述。dall-e-2 最大1000字符，dall-e-3 最大4000字符 |
| `model` | string | 否 | `dall-e-2` | 模型：`dall-e-2`、`dall-e-3` |
| `n` | integer | 否 | `1` | 生成图片数量，范围 1-10。dall-e-3 仅支持 n=1 |
| `quality` | string | 否 | `standard` | 图片质量：`standard`、`hd`（仅 dall-e-3） |
| `response_format` | string | 否 | `url` | 返回格式：`url`（URL有效期60分钟）、`b64_json` |
| `size` | string | 否 | `1024x1024` | 见下方尺寸支持 |
| `style` | string | 否 | `vivid` | 风格：`vivid`（超现实戏剧性）、`natural`（自然）。仅 dall-e-3 |
| `user` | string | 否 | - | 最终用户标识符，用于监控和检测滥用 |

**尺寸支持**：
- dall-e-2: `256x256`、`512x512`、`1024x1024`
- dall-e-3: `1024x1024`、`1792x1024`、`1024x1792`

#### 请求示例

```json
{
  "model": "dall-e-3",
  "prompt": "一只可爱的小海獭",
  "n": 1,
  "size": "1024x1024"
}
```

#### 成功响应 (200)

```json
{
  "created": 1753687182,
  "data": [
    {
      "revised_prompt": "A cute little sea otter...",
      "url": "https://dalleprodsec.blob.core.windows.net/..."
    }
  ]
}
```

| 响应字段 | 类型 | 说明 |
|----------|------|------|
| `created` | integer | 创建时间戳 |
| `data` | array | 图片对象列表 |
| `data[].url` | string | 图片URL（response_format=url 时） |
| `data[].b64_json` | string | Base64图片数据（response_format=b64_json 时） |
| `data[].revised_prompt` | string | 模型修改后的实际提示词 |
| `usage` | object | Token 使用统计（仅 gpt-image-1 模型） |

---

### 2.4 图像编辑 (Image Edits)

**用途**: 在给定原始图像和提示的情况下创建编辑或扩展图像。

```
POST /v1/images/edits
Content-Type: multipart/form-data
```

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|:----:|------|
| `image` | file(binary) | **是** | 要编辑的图像。必须为PNG，<4MB，正方形。无mask时需有透明通道 |
| `prompt` | string | **是** | 所需图像的文本描述，最大1000字符 |
| `mask` | file(binary) | 否 | 遮罩图像。PNG格式，<4MB，尺寸与image相同。透明区域为编辑位置 |
| `n` | string | 否 | 生成数量，1-10 |
| `size` | string | 否 | 尺寸：`256x256`、`512x512`、`1024x1024` |
| `response_format` | string | 否 | `url` 或 `b64_json` |
| `user` | string | 否 | 最终用户标识符 |
| `model` | string | 否 | 模型，如 `gpt-image-1` |

#### 请求示例 (cURL)

```bash
curl -X POST "https://api.openai.com/v1/images/edits" \
  -H "Authorization: Bearer $API_KEY" \
  -F "image=@original.png" \
  -F "prompt=合并两张图" \
  -F "mask=@mask.png" \
  -F "n=1" \
  -F "size=1024x1024"
```

#### 成功响应 (200)

```json
{
  "created": 1589478378,
  "data": [
    { "url": "https://..." }
  ]
}
```

---

### 2.5 嵌入向量 (Embeddings)

**用途**: 获取给定输入文本的向量表示，用于机器学习模型和算法。

```
POST /v1/embeddings
```

#### 请求参数

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|:----:|--------|------|
| `input` | string/array | **是** | - | 嵌入文本。支持字符串、字符串数组、token数组。最大token数8192（ada-002），数组维度≤2048 |
| `model` | string | **是** | - | 模型：`text-embedding-ada-002`、`text-embedding-3-small`、`text-embedding-3-large` |
| `encoding_format` | string | 否 | `float` | 嵌入格式：`float` 或 `base64` |
| `dimensions` | integer | 否 | - | 输出维度数（仅 text-embedding-3 及以上支持） |
| `user` | string | 否 | - | 最终用户标识符 |

**关键模型参数**：
| 模型 | 最大输入Token | 输出向量维度 |
|------|:------------:|:----------:|
| `text-embedding-ada-002` | 8,192 | 1,536 |
| `text-embedding-3-small` | - | 可通过 `dimensions` 调整 |
| `text-embedding-3-large` | - | 可通过 `dimensions` 调整 |

#### 请求示例

```json
{
  "input": "The food was delicious and the waiter...",
  "model": "text-embedding-ada-002",
  "encoding_format": "float"
}
```

#### 成功响应 (200)

```json
{
  "object": "list",
  "data": [
    {
      "object": "embedding",
      "embedding": [0.0023064255, -0.009327292, "...", -0.0028842222],
      "index": 0
    }
  ],
  "model": "text-embedding-ada-002",
  "usage": {
    "prompt_tokens": 8,
    "total_tokens": 8
  }
}
```

| 响应字段 | 类型 | 说明 |
|----------|------|------|
| `object` | string | 固定为 `list` |
| `data[].object` | string | 固定为 `embedding` |
| `data[].embedding` | number[] | 浮点数嵌入向量 |
| `data[].index` | integer | 嵌入在列表中的索引 |
| `usage.prompt_tokens` | integer | 提示使用token数 |
| `usage.total_tokens` | integer | 总token数 |

---

### 2.6 图生图（对话格式）

**用途**: 使用对话格式进行图生图操作。

```
POST /v1/chat/completions
```

#### 请求示例

```json
{
  "model": "sora_image",
  "messages": [
    {
      "role": "user",
      "content": [
        { "text": "画个类似的图片", "type": "text" },
        {
          "image_url": {
            "url": "https://filesystem.site/cdn/20250403/k0kVgLClcJyhH3Pybb5AInvsLptmQV.png"
          },
          "type": "image_url"
        }
      ]
    }
  ],
  "stream": true
}
```

#### 认证
Bearer Token: `Authorization: Bearer <YOUR_API_KEY>`

> 响应格式参考 OpenAI Chat Completions 标准。

---

### 2.7 文本转语音 (TTS)

**用途**: 将文本转换为语音。

```
POST /v1/audio/speech
```

#### 请求参数（基于示例推断）

| 参数名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| `model` | string | 语音模型 | `tts-1` |
| `input` | string | 要转换的文本 | `你好,世界!` |
| `voice` | string | 语音风格/发言人 | `alloy` |

#### 请求示例

```json
{
  "model": "tts-1",
  "input": "你好,世界!",
  "voice": "alloy"
}
```

> 注：该文档较简略，schema 未详细定义参数和响应结构。

---

### 2.8 语音转文本 (STT)

**用途**: 将音频文件转写为文本。

```
POST /v1/audio/transcriptions
Content-Type: multipart/form-data
```

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 | 示例值 |
|--------|------|:----:|------|--------|
| `file` | file(binary) | 是 | 待转写音频文件 | - |
| `model` | string | 是 | 语音识别模型 | `whisper-1` |

> 注：文档较简略，缺少 `language`、`prompt`、`response_format` 等参数定义。

---

## 三、Gemini 接口

### 3.1 对话格式 (Generate Content)

**用途**: Google Gemini API 对话生成。支持文本、图片、音频、代码、视频等多模态输入。支持函数调用、JSON模式、流式输出、代码执行等功能。

```
POST /v1beta/models/{model}:generateContent
```

#### 路径参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|:----:|------|
| `model` | string | **是** | 如 `gemini-2.0-flash` |

#### Query 参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|:----:|------|
| `key` | string | 否 | API密钥 |

#### 流式响应
将 `generateContent` 替换为 `streamGenerateContent` 即可：
```
POST /v1beta/models/{model}:streamGenerateContent
```

#### 请求体结构

```json
{
  "contents": [],           // 必需 - Content对象数组
  "tools": [],              // 可选 - Tool对象数组
  "toolConfig": {},         // 可选 - 工具配置
  "safetySettings": [],     // 可选 - SafetySetting数组
  "systemInstruction": {},  // 可选 - 系统指令
  "generationConfig": {},   // 可选 - 生成配置
  "cachedContent": ""       // 可选 - 缓存内容名称
}
```

#### Content 对象

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `parts` | array[Part] | **是** | 有序内容部分 |
| `role` | string | 否 | `user` / `model` / `function` / `tool` |

#### Part 类型

| 字段 | 类型 | 说明 |
|------|------|------|
| `text` | string | 纯文本 |
| `inlineData` | object | 内联媒体（mimeType + data base64） |
| `fileData` | object | 上传文件URI引用（mimeType + fileUri） |
| `functionCall` | object | 函数调用请求（name + args） |
| `functionResponse` | object | 函数调用响应（name + response） |
| `executableCode` | object | 可执行代码（language: `PYTHON` + code） |
| `codeExecutionResult` | object | 代码执行结果（outcome + output） |

#### generationConfig 关键参数

| 参数名 | 类型 | 说明 |
|--------|------|------|
| `stopSequences` | array[string] | 停止序列（最多5个） |
| `responseMimeType` | string | `text/plain` / `application/json` / `text/x.enum` |
| `responseSchema` | object | 输出架构 |
| `responseModalities` | array | `TEXT`、`IMAGE`、`VIDEO`、`AUDIO`、`DOCUMENT` |
| `candidateCount` | integer | 返回的回答数量 |
| `maxOutputTokens` | integer | 令牌上限 |
| `temperature` | number | [0.0, 2.0] 随机性控制 |
| `topP` | number | 核采样概率阈值 |
| `topK` | integer | 抽样令牌数量上限 |
| `seed` | integer | 解码种子 |
| `thinkingConfig` | object | 思考功能配置（includeThoughts + thinkingBudget） |
| `mediaResolution` | string | `MEDIA_RESOLUTION_LOW` / `MEDIUM` / `HIGH` |

#### safetySettings

| 参数 | 类型 | 说明 |
|------|------|------|
| `category` | string | 安全类别（共12种，如 `HARM_CATEGORY_HATE_SPEECH` 等） |
| `threshold` | string | `BLOCK_LOW_AND_ABOVE` / `BLOCK_MEDIUM_AND_ABOVE` / `BLOCK_ONLY_HIGH` / `BLOCK_NONE` / `OFF` |

#### 请求示例

**基础文本**：
```json
{
  "contents": [
    {
      "role": "user",
      "parts": [{ "text": "如何哄女朋友开心？" }]
    }
  ]
}
```

**图像分析**：
```json
{
  "contents": [
    {
      "role": "user",
      "parts": [
        { "text": "图中有什么？" },
        { "inlineData": { "mimeType": "image/jpeg", "data": "Base64..." } }
      ]
    }
  ]
}
```

**视频处理**：
```json
{
  "contents": [
    {
      "role": "user",
      "parts": [
        { "text": "总结视频内容" },
        { "inlineData": { "mimeType": "video/mp4", "data": "$VIDEO_B64" } }
      ]
    }
  ]
}
```

**PDF处理**：
```json
{
  "contents": [
    {
      "role": "user",
      "parts": [
        { "text": "介绍文档内容" },
        { "inlineData": { "mimeType": "application/pdf", "data": "Base64..." } }
      ]
    }
  ]
}
```

**代码执行**：
```json
{
  "contents": [
    {
      "role": "user",
      "parts": [{ "text": "计算斐波那契数列的第10项" }]
    }
  ],
  "tools": [{ "codeExecution": {} }]
}
```

**函数调用**：
```json
{
  "contents": [
    {
      "role": "user",
      "parts": [{ "text": "Schedule a meeting with Bob and Alice for 03/27/2025 at 10:00 AM about the Q3 planning." }]
    }
  ],
  "tools": [
    {
      "functionDeclarations": [
        {
          "name": "schedule_meeting",
          "description": "Schedules a meeting with specified attendees at a given time and date.",
          "parameters": {
            "type": "object",
            "properties": {
              "attendees": { "type": "array", "items": { "type": "string" } },
              "date": { "type": "string" },
              "time": { "type": "string" },
              "topic": { "type": "string" }
            },
            "required": ["attendees", "date", "time", "topic"]
          }
        }
      ]
    }
  ]
}
```

**JSON结构化输出**：
```json
{
  "contents": [
    {
      "role": "user",
      "parts": [{ "text": "List a few popular cookie recipes, and include the amounts of ingredients." }]
    }
  ],
  "generationConfig": {
    "responseMimeType": "application/json",
    "responseSchema": { ... }
  }
}
```

#### 响应结构

```json
{
  "candidates": [{
    "content": {
      "role": "model",
      "parts": [{ "text": "..." }]
    },
    "finishReason": "STOP",
    "index": 0,
    "safetyRatings": null
  }],
  "promptFeedback": { "safetyRatings": null },
  "usageMetadata": {
    "promptTokenCount": 5,
    "candidatesTokenCount": 838,
    "totalTokenCount": 843,
    "thoughtsTokenCount": 0,
    "promptTokensDetails": [{ "modality": "TEXT", "tokenCount": 5 }]
  }
}
```

**finishReason 枚举**：`STOP`、`MAX_TOKENS`、`SAFETY`、`RECITATION`、`LANGUAGE`、`OTHER`、`BLOCKLIST`、`PROHIBITED_CONTENT`、`SPII`、`MALFORMED_FUNCTION_CALL`、`IMAGE_SAFETY`

**blockReason 枚举**：`SAFETY`、`OTHER`、`BLOCKLIST`、`PROHIBITED_CONTENT`、`IMAGE_SAFETY`

---

### 3.2 Gemini 生图

**用途**: 用于支持 nano-banana 原生格式生图。使用 Gemini 2.5 Flash Image 模型。

```
POST /v1beta/models/gemini-2.5-flash-image:generateContent
```

#### 请求示例

```json
{
  "contents": [
    {
      "parts": [
        {
          "text": "Create a picture of a nano banana dish in a fancy restaurant with a Gemini theme"
        }
      ]
    }
  ],
  "generationConfig": {
    "imageConfig": {
      "aspectRatio": "16:9",
      "imageSize": "2K"
    }
  }
}
```

**参考示例代码**: [Gemini-Nano生图完整示例.zip](https://www.juhenext.com/wp-content/uploads/2025/11/Gemini-Nano生图完整示例.zip)

---

### 3.3 Gemini 改图

**用途**: 用于支持 nano-banana 原生格式生图（传入参考图进行修改）。

```
POST /v1beta/models/gemini-2.5-flash-image:generateContent
```

#### 请求示例

```json
{
  "contents": [
    {
      "parts": [
        {
          "text": "Create a picture of my cat eating a nanobanana in a fancy restaurant under the Gemini constellation"
        },
        {
          "inline_data": {
            "mime_type": "image/jpeg",
            "data": "$IMG_BASE64"
          }
        }
      ]
    }
  ]
}
```

---

## 四、Anthropic 接口

### 4.1 Messages API

**用途**: Anthropic Claude 对话接口。给定包含文本和/或图像内容的结构化输入消息列表，模型生成下一条消息。可用于单次查询或无状态多轮对话。

```
POST /v1/messages
```

#### 请求头

| 参数名 | 类型 | 必填 | 说明 | 示例值 |
|--------|------|:----:|------|--------|
| `anthropic-version` | string | 否 | API版本控制 | `2023-06-01` |
| `Content-Type` | string | 否 | 内容类型 | `application/json` |
| `x-api-key` | string | 是 | API Key认证 | `sk-ant-api03-xxx` |

#### 请求参数（必填）

| 参数名 | 类型 | 约束 | 说明 |
|--------|------|------|------|
| `max_tokens` | integer | ≥ 2 | 生成的最大 token 数量 |
| `messages` | array | ≥ 1 项 | 输入消息列表 |
| `model` | string | 长度 1-256 | 模型名称 |

#### 请求参数（可选）

| 参数名 | 类型 | 默认值 | 说明 |
|--------|------|--------|------|
| `metadata` | object | - | 请求元数据，含 `user_id` 字段 |
| `stop_sequences` | array[string] | - | 自定义停止序列 |
| `stream` | boolean | - | 是否 SSE 增量流式返回 |
| `system` | string | - | 系统 prompt |
| `temperature` | number | 1 | (0,1) 随机性控制 |
| `thinking` | object | - | 扩展思考功能配置 |
| `tool_choice` | object | - | 工具选择策略（auto/any/tool） |
| `tools` | array | - | 工具定义列表 |
| `top_k` | integer | - | 前K采样 |
| `top_p` | number | - | 核采样概率阈值 |

#### messages 消息对象

| 字段 | 类型 | 必填 | 说明 |
|------|------|:----:|------|
| `role` | string | **是** | `user` / `assistant` |
| `content` | string/array | **是** | 消息内容 |

**content 内容块支持类型**：

| 类型 | 说明 |
|------|------|
| `text` | 文本内容块 |
| `image` | 图片内容块（source.type=base64，支持 image/jpeg/png/gif/webp） |
| `tool_use` | 工具使用块（id + name + input） |
| `tool_result` | 工具结果块（tool_use_id + content + is_error） |

#### thinking 思考模式

| 模式 | 字段 | 说明 |
|------|------|------|
| **启用** | `type: "enabled"`, `budget_tokens` (最小1024) | Claude内部推理token数 |
| **禁用** | `type: "disabled"` | 关闭思考 |

#### tools 工具定义

| 类型 | 说明 |
|------|------|
| `custom` | 自定义工具（name + description + input_schema） |
| `computer_20241022` | 计算机控制工具（display_width_px + display_height_px） |
| `bash_20241022` | Bash 终端工具 |
| `text_editor_20241022` | 文本编辑器工具 |

#### 请求示例

**基础对话**：
```json
{
  "model": "claude-sonnet-4-20250514",
  "max_tokens": 1024,
  "messages": [
    { "role": "user", "content": "Hello, world" }
  ]
}
```

**图像分析**：
```json
{
  "model": "claude-sonnet-4-20250514",
  "messages": [
    {
      "role": "user",
      "content": [
        { "type": "image", "source": { "type": "base64", "media_type": "image/jpeg", "data": "/9j/4AAQSkZJRg..." } },
        { "type": "text", "text": "这张图片里有什么?" }
      ]
    }
  ]
}
```

**工具调用**：
```json
{
  "model": "claude-sonnet-4-20250514",
  "messages": [{ "role": "user", "content": "今天北京的天气怎么样?" }],
  "tools": [
    {
      "name": "get_weather",
      "description": "获取指定位置的当前天气",
      "input_schema": {
        "type": "object",
        "properties": { "location": { "type": "string", "description": "城市名称,如:北京" } },
        "required": ["location"]
      }
    }
  ]
}
```

**思考模式**：
```json
{
  "model": "claude-sonnet-4-20250514",
  "max_tokens": 16000,
  "thinking": { "type": "enabled", "budget_tokens": 10000 },
  "messages": [{ "role": "user", "content": "如何哄女朋友开心？" }]
}
```

#### 响应格式（非流式）

```json
{
  "content": [
    { "text": "Hi! My name is Claude.", "type": "text" }
  ],
  "id": "msg_013Zva2CMHLNnXjNJKqJ2EF",
  "model": "claude-sonnet-4-20250514",
  "role": "assistant",
  "stop_reason": "end_turn",
  "stop_sequence": null,
  "type": "message",
  "usage": {
    "input_tokens": 2095,
    "output_tokens": 503,
    "cache_creation_input_tokens": 0,
    "cache_read_input_tokens": 0
  }
}
```

| 响应字段 | 类型 | 说明 |
|----------|------|------|
| `content` | array | 生成内容块数组 |
| `id` | string | 响应唯一标识 |
| `model` | string | 实际模型名 |
| `role` | string | 固定为 `assistant` |
| `stop_reason` | string | `end_turn` / `max_tokens` / `stop_sequence` / `tool_use` |
| `stop_sequence` | string/null | 触发的停止序列 |
| `type` | string | 固定为 `message` |
| `usage` | object | token使用统计 |

**流式响应 SSE 事件类型**：`message_start` → `content_block_start` → `content_block_delta` → `content_block_stop` → `message_delta` → `message_stop`

---

## 五、图片生成

### 5.1 豆包/即梦出图 - Chat格式

**用途**: 使用豆包/即梦模型出图，通过 Chat Completions 格式。**务必参考参数说明，否则会出现空返、错误返回等情况！**

```
POST /v1/chat/completions
```

#### 请求头

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|:----:|------|
| `Authorization` | string | 否 | 豆包绘图分组 key |
| `Content-Type` | string | 否 | `application/json` |

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|:----:|------|
| `model` | string | **是** | `doubao-seedream-4-0-250828` |
| `messages` | array[object] | **是** | 对话消息数组 |
| `temperature` | number | **是** | 温度参数 |
| `stream` | boolean | **是** | 是否流式输出 |

**绘画参数写规则**：参数写入 `content` 字段内，以 `-参数名=值` 格式，各参数之间**必须有空格分隔**。

```
画一只猫,在草原奔跑 -size=2k -n=2 -type=normal -watermark=false
```

**支持的 content 参数**：

| 参数名 | 说明 | 可选值 | 默认值 |
|--------|------|--------|--------|
| `-type` | 绘画类型 | `normal`（最大4张）、`group`（最大10张） | `normal` |
| `-n` | 出图数量 | 正整数 | `1` |
| `-watermark` | 水印控制 | `true` / `false` | `false` |
| `-size` | 图片大小 | 见下方尺寸规范 | `1k` |

**尺寸规范**：
- **档位形式**: `1k`、`2k`、`4k`
- **像素形式**: `宽度x高度`（宽1280~4096，高720~4096，宽高比[1/16,16]），不合法退回 `1024x1024`
- **比例形式**: 自动映射

| 宽高比 | 1:1 | 4:3 | 3:4 | 16:9 | 9:16 | 3:2 | 2:3 |
|--------|-----|-----|-----|------|------|-----|-----|
| 推荐像素 | 2048x2048 | 2304x1728 | 1728x2304 | 2560x1440 | 1440x2560 | 2496x1664 | 1664x2496 |

#### 请求示例

```json
{
  "messages": [
    {
      "role": "user",
      "content": "画一只猫,在草原奔跑 -size=2k -n=2 -type=normal -watermark=false"
    }
  ],
  "model": "doubao-seedream-4-0-250828",
  "temperature": 0.8,
  "stream": false
}
```

> 状态: **developing**

---

### 5.2 豆包/即梦出图 - Image格式（普通生图）

**用途**: 使用 DALL·E 兼容格式调用豆包/即梦出图（普通生图）。参考官方文档: https://www.volcengine.com/docs/82379/1541523

```
POST /v1/images/generations
```

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|:----:|------|
| `model` | string | **是** | 固定值 `doubao-seedream-4-0-250828` |
| `prompt` | string | **是** | 绘画提示词 |
| `n` | integer | **是** | 出图数量，默认1。normal最大4张，group最大10张 |
| `size` | string | **是** | 图片大小，默认1k |

**额外参数**（未在schema中但文档提及）：

| 参数 | 类型 | 说明 |
|------|------|------|
| `type` | string | `normal`（默认，最大4张）或 `group`（最大10张） |
| `watermark` | boolean | 水印控制，默认 false |

**尺寸规范**：同 5.1 节。

#### 请求示例

```json
{
  "model": "doubao-seedream-4-0-250828",
  "prompt": "一只可爱的猫",
  "n": 1,
  "size": "1k"
}
```

> 状态: **developing**

---

### 5.3 豆包/即梦出图 - Image格式（高质量生图）

**用途**: 豆包/即梦高质量出图，DALL·E 兼容格式。

```
POST /v1/images/generations
```

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|:----:|------|
| `model` | string | **是** | `doubao-seedream-4-0-250828` |
| `prompt` | string | **是** | 绘画提示词 |
| `n` | integer | **是** | 出图数量，默认1 |
| `type` | string | **是** | `normal`（最大4张）/ `group`（最大10张） |
| `size` | string | **是** | 图片大小，默认1k |

尺寸规范同 5.1 节。额外参数 `watermark`（默认 false）。

#### 请求示例

```json
{
  "model": "doubao-seedream-4-0-250828",
  "prompt": "一只可爱的小海獭",
  "n": 2,
  "type": "normal",
  "size": "1024x1024"
}
```

> 状态: **developing**

---

### 5.4 豆包/即梦出图 - Image格式（图片编辑）

**用途**: 豆包/即梦图片编辑，传入底图和提示词进行修改。

```
POST /v1/images/edits
Content-Type: multipart/form-data
```

#### 请求参数

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|:----:|--------|------|
| `model` | string | **是** | - | 固定值 `doubao-seedream-4-0-250828` |
| `image` | file(binary) | 否 | - | 底图文件 |
| `prompt` | string | **是** | - | 绘画提示词 |
| `n` | string | 否 | `1` | 出图数量 |
| `type` | string | 否 | `normal` | 绘画类型 |
| `size` | string | 否 | `1k` | 图片大小 |
| `watermark` | boolean | 否 | `false` | 水印控制 |

#### 请求示例

```json
{
  "model": "doubao-seedream-4-0-250828",
  "image": "file://C:\\Users\\Administrator\\Desktop\\1.png",
  "prompt": "一个人骑在猪上",
  "n": "2",
  "type": "normal",
  "size": "2048x2048"
}
```

> 状态: **developing**

---

### 5.5 gpt-image-2 生成图片 1K

**用途**: GPT-Image-2 模型生成图片，仅支持1K分辨率。通过提示词控制图片尺寸。

```
POST /v1/images/generations
```

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|:----:|------|
| `prompt` | string | **是** | 图片描述（通过内容控制尺寸） |
| `model` | string | **是** | `gpt-image-2` |

#### 请求示例

```json
{
  "model": "gpt-image-2",
  "prompt": "画一只猫在抖音直播间直播,图片尺寸为16:9"
}
```

> 状态: **developing**

---

### 5.6 gpt-image-2 生成图片 124K

**用途**: GPT-Image-2 模型生成图片，通过 `size` 参数控制尺寸。

```
POST /v1/images/generations
```

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|:----:|------|
| `prompt` | string | **是** | 图片描述 |
| `model` | string | **是** | `gpt-image-2-pro` |
| `size` | string | **是** | 图片尺寸 |
| `image` | array[string] | **是** | URL数组或Base64数组（参考图） |

**支持的尺寸**：

| 尺寸 | 说明 |
|------|------|
| `1024x1024` | 正方形 |
| `1536x1024` | 横向 |
| `1024x1536` | 纵向 |
| `2048x2048` | 2K 正方形 |
| `2048x1152` | 2K 横向 |
| `3840x2160` | 4K 横向 |
| `2160x3840` | 4K 纵向 |
| `auto` | 默认自动 |

> 状态: **developing**

---

### 5.7 gpt-image-2 编辑图片/图生图

**用途**: GPT-Image-2 模型编辑图片/图生图。支持 URL 或 Base64 参考图，也可使用 form-data 直接上传文件。

```
POST /v1/images/edits
```

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|:----:|------|
| `prompt` | string | **是** | 图片描述 |
| `model` | string | **是** | `gpt-image-2` 或 `gpt-image-2-pro` |
| `image` | array[string] | **是** | 参考图。URL/Base64数组，支持 form-data file 上传 |

> 状态: **developing**

---

## 六、视频生成

### 6.1 Veo3 异步视频生成

**用途**: Veo3 模型异步视频生成。提交后返回任务对象，需轮询获取最终视频。

```
POST /v1/videos
Content-Type: multipart/form-data
```

#### 请求参数

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|:----:|--------|------|
| `model` | string | **是** | - | `veo_3_1-fast` |
| `prompt` | string | **是** | - | 提示词 |
| `size` | string | 否 | `720x1280` | 尺寸：`720x1280`（竖屏）/ `1280x720`（横屏） |
| `seconds` | integer | 否 | - | 视频时长（秒），如 `10` |
| `input_reference` | file(binary) | 否 | - | 图片参考（File格式） |
| `image` | string | 否 | - | 图片参考（Base64） |
| `images` | array[string] | 否 | - | 多图数组（Base64数组） |

#### 响应 (200)

```json
{
  "id": "video_55c77134-3fbe-4d5d-839f-c9af4556405b",
  "object": "video",
  "model": "veo_3_1-fast",
  "status": "queued",
  "progress": 0,
  "created_at": 1762600200,
  "seconds": "10",
  "size": "720x720"
}
```

> 状态: **developing**

---

### 6.2 Sora-2 创建角色

**用途**: 基于视频创建 Sora-2 角色。角色创建后可通过 `@{username}` 在提示词中引用。

```
POST /sora/v1/characters
```

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|:----:|------|
| `model` | string | **是** | `sora-2` |
| `url` | string | 否 | 视频URL（含需创建的角色） |
| `timestamps` | string | 否 | 角色出现时间，格式 `"开始,结束"`，范围1~3秒 |
| `from_task` | string | 否 | 已完成任务ID（可根据已生成任务创建角色） |

#### 请求示例

```json
{
  "model": "sora-2",
  "url": "https://image-plus.oss-cn-beijing.aliyuncs.com/sora/0007dfc9-1a5d-4492-b8bd-42498617880f.mp4",
  "timestamps": "1,3"
}
```

#### 响应 (200)

| 字段 | 类型 | 说明 |
|------|------|------|
| `id` | string | 角色ID |
| `username` | string | 角色名称（用于 `@{username}` 引用） |
| `permalink` | string | OpenAI 角色主页 |
| `profile_picture_url` | string | 角色头像URL |

> 状态: **developing**

---

### 6.3 Sora-2 获取视频任务状态

**用途**: 根据任务ID查询 Sora-2 视频生成任务状态。

```
GET /v1/videos/{id}
```

#### 路径参数

| 参数名 | 类型 | 必填 | 说明 | 示例值 |
|--------|------|:----:|------|--------|
| `id` | string | **是** | 任务ID | `sora-2:task_01k770gx3je7dtxwmmph7efpcw` |

> 状态: **released**（但响应结构未完整定义）

---

### 6.4 Sora-2 获取视频内容

**用途**: 根据任务ID获取生成完成的视频内容。

```
GET /v1/videos/{id}/content
```

#### 路径参数

| 参数名 | 类型 | 必填 | 示例值 |
|--------|------|:----:|--------|
| `id` | string | **是** | `sora-2:task_01k87fkhrse8y8wdwjqf17b1ek` |

> 注：URL 中的 `:` 需编码为 `%3A`

> 状态: **released**（但响应结构未完整定义）

---

### 6.5 Sora-2 编辑视频 (Remix)

**用途**: 对已生成的视频进行二次编辑（Remix）。

```
POST /v1/videos/{id}/remix
```

#### 路径参数

| 参数名 | 类型 | 必填 | 示例值 |
|--------|------|:----:|--------|
| `id` | string | **是** | `sora-2:task_01k76rbx40e7p9gzexm129v1z2` |

#### 请求体

```json
{
  "prompt": "让这个视频背景变成蓝天白云"
}
```

> 状态: **developing**

---

### 6.6 Sora-2 创建视频（带Character）

**用途**: Sora-2 异步生成视频，支持附带角色创建。

```
POST /v1/videos
Content-Type: multipart/form-data
```

#### 请求参数

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|:----:|--------|------|
| `model` | string | **是** | - | `sora-2`（标清）/ `sora-2-hd`（高清，仅pro） |
| `prompt` | string | **是** | - | 提示词 |
| `seconds` | string | 否 | - | 视频时长（秒），如 `10` |
| `input_reference` | file(binary) | 否 | - | 参考图像 |
| `size` | string | 否 | - | `1024x1792`/`1792x1024`（高清，仅pro）、`720x1280`/`1280x720`（标清） |
| `watermark` | boolean | 否 | 无 | 是否水印 |
| `private` | boolean | 否 | `false` | 是否隐藏视频。true时视频不发布且无法remix |
| `character_url` | string | 否 | - | 创建角色的视频链接（**不能出现真人**） |
| `character_timestamps` | string | 否 | - | 角色时间范围 `"start,end"`，差值1~3秒 |
| `character_from_task` | string | 否 | - | 已完成任务ID |

> 状态: **developing**

---

### 6.7 Sora-2 从任务中创建角色

**用途**: 基于已完成视频任务创建角色。

```
POST /v1/videos
Content-Type: multipart/form-data
```

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|:----:|------|
| `model` | string | **是** | 固定值 `sora-2-character` |
| `character_from_task` | string | **是** | 任务ID，格式 `sora-2:task_{id}` |
| `chacter_timestamps` | string | 否 | 角色时间范围 `"start,end"`，差值1~3秒 |

#### 响应 (200)

| 字段 | 类型 | 说明 |
|------|------|------|
| `id` | string | 角色ID |
| `username` | string | 角色名称 |
| `permalink` | string | 角色主页 |
| `profile_picture_url` | string | 角色头像URL |

> 状态: **developing**

---

### 6.8 Sora-2 故事板创建视频

**用途**: 使用故事板（多个分镜）格式创建 Sora-2 视频。

```
POST /v1/videos
Content-Type: multipart/form-data
```

#### 请求参数

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|:----:|--------|------|
| `model` | string | **是** | - | `sora-2` / `sora-2-hd`（高清仅pro） |
| `prompt` | string | **是** | - | 故事板格式提示词 |
| `seconds` | string | 否 | - | 视频时长：`10`/`15`/`25`（25仅pro） |
| `input_reference` | file(binary) | 否 | - | 参考图 |
| `size` | string | 否 | - | 分辨率 |
| `watermark` | boolean | 否 | 无 | 水印 |
| `private` | boolean | 否 | `false` | 隐藏视频 |
| `character_url` | string | 否 | - | 角色视频链接 |
| `character_timestamps` | string | 否 | - | 角色时间范围 |
| `metadata` | string | 否 | - | 元数据 |
| `character_from_task` | string | 否 | - | 任务ID创建角色 |
| `character_create` | boolean | 否 | - | 视频生成后自动创建角色 |

**故事板 prompt 格式**（必须严格遵循）：

```
Shot 1:
duration: 7.5sec
Scene: 飞机起飞

Shot 2:
duration: 7.5sec
Scene: 飞机降落
```

> 状态: **developing**

---

### 6.9 Grok 视频生成

**用途**: Grok Imagine 模型视频生成。

```
POST /v1/videos
Content-Type: multipart/form-data
```

#### 请求参数

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|:----:|--------|------|
| `model` | string | **是** | - | `grok-imagine-1.0-video` |
| `prompt` | string | **是** | - | 提示词 |
| `aspect_ratio` | string | 否 | - | 宽高比：`16:9` / `9:16` / `1:1` / `2:3` / `3:2` |
| `video_length` | integer | 否 | - | 视频时长：`6` 或 `10` |
| `file` | file(binary) | 否 | - | 图片参考（File格式） |
| `image` | string | 否 | - | 图片参考（Base64） |
| `images` | array[string] | 否 | - | 多图数组（Base64数组） |
| `resolution_name` | string | 否 | - | 分辨率：`480p` / `720p` |
| `preset` | string | 否 | - | 风格：`fun` / `normal` / `spicy` / `custom` |

#### 响应 (200)

```json
{
  "id": "video_55c77134-3fbe-4d5d-839f-c9af4556405b",
  "object": "video",
  "model": "veo_3_1-fast",
  "status": "queued",
  "progress": 0,
  "created_at": 1762600200,
  "seconds": "10",
  "size": "720x720"
}
```

> 状态: **released**  
> 注意：响应示例中 model 返回 `veo_3_1-fast` 与实际请求模型不一致，可能为文档编写不统一。

---

### 6.10 Seedance 2.0 异步视频生成

**用途**: Seedance 2.0 异步视频生成。支持多种模型（veo、sora-2 等）。

```
POST /v1/videos
Content-Type: multipart/form-data
```

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|:----:|------|
| `model` | string | **是** | 如 `sora-2`、`veo_3_1-fast` |
| `prompt` | string | **是** | 提示词 |
| `size` | string | 否 | `720x1280`（竖屏）/ `1280x720`（横屏） |
| `seconds` | integer | 否 | 视频时长 |
| `input_reference` | file(binary) | 否 | 图片参考（File格式，可多张） |
| `character_url` | string | 否 | 角色视频链接（不能出现真人） |
| `character_timestamps` | string | 否 | 角色时间范围 `"start,end"`，差值1~3秒 |
| `image` | string | 否 | 图片参考（Base64） |
| `images` | array[string] | 否 | 多图数组（Base64数组） |

#### 响应 (200)

```json
{
  "id": "video_bbfbc1d2-ab22-44ca-b9dd-bc16983acac2",
  "object": "video",
  "model": "sora_video2",
  "status": "queued",
  "progress": 0,
  "created_at": 1761635478,
  "size": "720x720"
}
```

**status 枚举**：`queued` → `processing` → `completed` / `failed` / `cancelled`

> 状态: **developing**

---

### 6.11 Seedance 2.0 任务查询进度

**用途**: 查询 Seedance 2.0 视频生成任务进度。

```
GET /v1/videos/{task_id}
```

#### 路径参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|:----:|------|
| `task_id` | string | **是** | 任务ID |

#### 响应 (200)

| 字段 | 类型 | 说明 |
|------|------|------|
| `id` | string | 任务ID |
| `object` | string | 对象类型 |
| `model` | string | 使用的模型 |
| `status` | string | 任务状态 |
| `progress` | integer | 进度百分比 |
| `created_at` | integer | 创建时间戳 |
| `completed_at` | integer | 完成时间戳 |
| `expires_at` | integer | 过期时间戳 |
| `seconds` | string | 视频时长 |
| `size` | string | 视频大小 |
| `remixed_from_video_id` | string | 源视频ID |
| `error` | object | 错误信息（message + code） |
| `video_url` | string | 视频地址 |

> 状态: **released**

---

### 6.12 通用 - 任务查询结果

**用途**: 根据任务ID获取视频生成结果内容。

```
GET /v1/videos/{task_id}/content
```

#### 路径参数

| 参数名 | 类型 | 必填 | 示例值 |
|--------|------|:----:|--------|
| `task_id` | string | **是** | `task_64bca5c41f79` |

> 状态: **released**（响应结构未完整定义）

---

### 6.13 通用 - 任务查询进度

**用途**: 通用视频生成任务进度查询接口。

```
GET /v1/videos/{task_id}
```

响应结构与 6.11 类似，包含 `id`、`status`、`progress`、`video_url`、`error` 等完整字段。

> 状态: **released**

---

### 6.14 通用 - 异步视频生成

**用途**: 通用异步视频生成接口。支持 veo、sora-2 等多种模型。

```
POST /v1/videos
Content-Type: multipart/form-data
```

#### 请求参数

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|:----:|------|
| `model` | string | **是** | 如 `sora-2`、`veo_3_1-fast` 等 |
| `prompt` | string | **是** | 提示词 |
| `size` | string | 否 | 尺寸 |
| `seconds` | integer | 否 | 视频时长 |
| `input_reference` | file(binary) | 否 | 图片参考 |
| `character_url` | string | 否 | 角色视频链接 |
| `character_timestamps` | string | 否 | 角色时间范围 |
| `image` | string | 否 | 图片Base64 |
| `images` | array[string] | 否 | 多图Base64数组 |

#### 响应 (200)

```json
{
  "id": "video_bbfbc1d2-ab22-44ca-b9dd-bc16983acac2",
  "object": "video",
  "model": "sora_video2",
  "status": "queued",
  "progress": 0,
  "created_at": 1761635478,
  "size": "720x720"
}
```

**status 枚举**：`queued` / `processing` / `completed` / `failed` / `cancelled`

> 状态: **developing**

---

## 附录：视频生成通用流程

所有视频生成接口（Veo3、Sora-2、Grok、Seedance）遵循统一的异步工作流：

1. **提交任务**: `POST /v1/videos` → 获得 `id` 和初始 `status: "queued"`
2. **查询进度**: `GET /v1/videos/{id}` → 获取 `status`、`progress`
3. **获取结果**: `GET /v1/videos/{id}/content` → 获取视频内容

**状态流转**: `queued` → `processing` → `completed` / `failed` / `cancelled`

---

> **文档整理完成。** 共收录 33 个 API 接口的完整技术细节。  
> 原始来源: https://chaiapi.apifox.cn/  
> 部分接口状态为 `developing`，实际使用前建议通过 Apifox 在线调试确认最新情况。

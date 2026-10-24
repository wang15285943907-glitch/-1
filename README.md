# 逐句精读 · 英语阅读讲义排版器

一个不需要构建工具的静态网页项目：把阅读题照片发给当前 ChatGPT 对话，让助手先联网查找并核对官方 PDF；再把已确认的 PDF 文字层或 ChatGPT 生成的讲义 JSON 导入网页，排成适合屏幕阅读和打印的逐句对照讲义。

## 使用方式

1. 用浏览器打开 `index.html`。本地运行时可在项目目录启动 `python -m http.server 8080`，然后访问 `http://localhost:8080`。
2. 在当前 ChatGPT 对话上传阅读题照片；在网页填写已知的年份、科目和文章编号，点“复制官方 PDF 检索指令”并发到同一对话。
3. ChatGPT 先联网搜索并打开官方 PDF，核对发布机构、年份、科目、文章编号、文章和题目。找不到可核验的官方 PDF 或线索不足时，先说明并询问，不使用 OCR 或培训机构转载替代原文。
4. 可把找到的官方 PDF 下载后传到网页，点击“提取 PDF 文字”。页面只读取 PDF 自带的文字层；扫描图片 PDF 没有文字层时会停止并提示，不运行 OCR。
5. 把 ChatGPT 返回的 JSON 导入或粘贴到“讲义数据”编辑器，点击“应用并排版”。
6. 点击“打印 / 保存 PDF”，在浏览器打印窗口选择“另存为 PDF”。

页面会将英文原句与中文翻译逐句相邻排版；重点词标红；每段词汇与短语放在右侧；题目答案项和对应原文证据分别着色，并提供从题目跳回证据的链接。讲义可显示 PDF 标题、发布机构、官方链接和来源核验说明。窄屏下，词汇侧注会移到段落下方。

## 讲义 JSON 结构

```json
{
  "title": "文章标题",
  "exam": "考试科目 / 年份 / Text",
  "year": "年份",
  "sourcePdfUrl": "官方 PDF 链接",
  "sourceTitle": "官方 PDF 标题",
  "sourcePublisher": "发布机构",
  "sourceVerificationNote": "通过年份、科目、文章编号和题目内容核验",
  "centralIdea": "全文中心思想",
  "logicChain": ["段落逻辑"],
  "paragraphs": [
    {
      "label": "P1",
      "gist": "本段段意",
      "sentences": [
        {
          "english": "完整英文原句",
          "translation": "对应中文翻译",
          "structure": "长难句结构拆解",
          "keyWords": ["重点词"]
        }
      ],
      "keyWords": [{ "word": "单词", "meaning": "词义 / 熟词生义" }],
      "keyPhrases": ["固定短语 · 中文义"]
    }
  ],
  "questions": [
    {
      "number": 1,
      "type": "题型",
      "stem": "题干原文",
      "stemTranslation": "题干翻译",
      "options": [
        { "label": "A", "text": "选项原文", "translation": "选项翻译", "reasoning": "该项判断依据" }
      ],
      "answer": "B",
      "evidence": { "paragraph": "P1", "sentence": 1, "quote": "原文逐字证据" },
      "analysis": "正确项逻辑",
      "trap": "干扰项陷阱"
    }
  ],
  "takeaway": "本篇阅读方法总结"
}
```

## 实现说明

- 页面不调用 AI API，也不做图片 OCR。当前 ChatGPT 对话负责联网查找、打开并核验官方 PDF，以及生成逐句翻译和题目解析；网页负责读取用户下载的 PDF 文字层、标注、排版和 PDF 打印。
- 官方 PDF 由浏览器本地解析；PDF.js 从公共 CDN 按需加载。扫描版 PDF 没有可复制文字层时，页面停止提取，不会用 OCR 补字。讲义 JSON 保存于当前浏览器的本地存储，不发送给项目服务器。
- 原始照片请上传到 ChatGPT 对话作定位线索。回答必须报告官方 PDF 标题、发布机构、URL 和匹配依据；无法核验时先询问，不以 OCR、搜索摘要或非官方转载代替。
- 示例文章是为展示排版制作的虚构内容，不是考研真题。

# 逐句精读 · 英语阅读讲义排版器

一个不需要构建工具的静态网页项目：上传阅读题照片或 PDF，在浏览器中提取文字；导入 ChatGPT 按精读流程生成的 JSON，排成适合屏幕阅读和打印的逐句对照讲义。

## 使用方式

1. 用浏览器打开 `index.html`。本地运行时可在项目目录启动 `python -m http.server 8080`，然后访问 `http://localhost:8080`。
2. 上传照片或官方 PDF，点击“提取原文”，在识别框里校正 OCR 结果。
3. 在当前 ChatGPT 对话上传原图，点击“复制精读指令”并发送。指令要求先核对官方 PDF，再按完整逐句翻译、长难句拆解、段落词汇、逐题证据和选项分析的习惯输出 JSON。
4. 把 ChatGPT 返回的 JSON 导入或粘贴到“讲义数据”编辑器，点击“应用并排版”。
5. 点击“打印 / 保存 PDF”，在浏览器打印窗口选择“另存为 PDF”。

页面会将英文原句与中文翻译逐句相邻排版；重点词标红；每段词汇与短语放在右侧；题目答案项和对应原文证据分别着色，并提供从题目跳回证据的链接。窄屏下，词汇侧注会移到段落下方。

## 讲义 JSON 结构

```json
{
  "title": "文章标题",
  "exam": "考试科目 / 年份 / Text",
  "year": "年份",
  "sourcePdfUrl": "官方 PDF 链接",
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

- 页面不调用 AI API。ChatGPT 负责联网核对官方原文并生成翻译和题目解析；网页负责 OCR、标注、排版和 PDF 打印。
- 图片/PDF 由浏览器本地读取，OCR 所需的 Tesseract.js 与 PDF.js 运行库从公共 CDN 按需加载。识别内容、讲义 JSON 保存于当前浏览器的本地存储，不发送给项目服务器。
- OCR 结果只作校对底稿；请把原始照片也上传到 ChatGPT 对话。照片和官方 PDF 不一致时，由官方原文为准；无法核实时应保留不确定标记。
- 示例文章是为展示排版制作的虚构内容，不是考研真题。

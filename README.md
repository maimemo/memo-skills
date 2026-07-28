# 墨墨背单词 Skills

## 安装
```bash
npx skills add maimemo/memo-skills
```

## Skills

### memo-api 

开放平台 API skill，参考 https://open.maimemo.com 把 Token 放到环境变量 `MAIMEMO_TOKEN` 中

#### Examples
- 我今天墨墨的学习进度？
- 今天忘记和模糊的单词有哪些？
- 明天一共有多少单词要复习？
- 我总共背了多少单词？
- apple 这个单词下次什么时候复习？
- 把 apple 这个单词加到我的云词本中
- 列出 apple 单词下我创建的所有内容
- 给 apple 创建一个助记/释义/例句：balabala
- 找出来这篇文章中我还没背的单词，加入到云词本 https://example.com

### markji-card-syntax

Markji（墨墨记忆卡）卡片语法 skill，使用中文生成、改写、检查和修正 Markji 卡片内容。

#### Examples
- 帮我用 Markji 语法做个化学元素周期表的卡片
- 把这段笔记转成墨墨记忆卡
- 帮我检查这张卡片语法有没有问题
- 这张卡片我加了个挖空但不生效，帮我看看
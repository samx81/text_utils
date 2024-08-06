# 文本處理工具 (中文斷句修改版)

**修改部分**

- 斷句不再將 "有引號的子句" 視為前後主要句子的其中一個字
- 子句太長時會先將已暫存的句子切開，再繼續處理子句

#### 範例
```
from text_utils.segmenter import SentenceSegmenter
ss = SentenceSegmenter(token_limits=20) 
# token limit != len(sentence), token_num 會是 `char_num * 1.5`

text = "李洋在社群平台表示，「最後一舞，這是送給你們的」、「下台一鞠躬，希望最後這個禮物你們喜歡」，並附上自己與 2024 巴黎奧運金牌的合照。"
# 修改前這句會原封不動保留

for sent in ss.segment(text):
    print("tokens", len(sent), sent)

# Result:
# tokens 25 李洋在社群平台表示，「最後一舞，這是送給你們的」、
# tokens 43 「下台一鞠躬，希望最後這個禮物你們喜歡」，並附上自己與 2024 巴黎奧運金牌的合照。

## 第二段的引號子句個數為 18，仍然在 token limit 內
```

---
# 文本处理工具脚本库


## 中文断句

使用的中文分句算法支持**普通文本**的句子切分和**嵌套双引号**的句子切分两种形式。

- 支持普通文本的句子切分
普通的文本切分就是直接按照句子断句的标点符号进行切分。当句子中包含双引号标点时，如果双引号中的内容并不包含断句符号，则认为双引号中的内容属于原句成分，也当做普通句子进行切分即可。

- 支持嵌套双引号的句子切分
但当嵌套双引号中内容包括断句标点，则需要分情况考虑。我们定义了一个参数，用于控制引号中单个句子的边际长度。当引号中部分被断句符号切开后，长度小于边际长度时，我们认为这一处断句符号不应切分句子。反之，需要根据断句符号进行切分，并且此时双引号也将被视为断句符号，即上引号前面的部分单独成句，下引号后面的部分单独成句。

- `模糊`限定句子的最长单词个数
对于不规则文本，很难通过标准断句标点分句。这里我们引入了句子的长度限制，当句子长度超过这个限制后，我们将使用普通标点符号分割句子，例如逗号，分号等。[注意]: 长度限制不是严格限制的，当设置句子最大长度为20时，有可能某个分句的实际长度是23。

**断句使用方法**

```
from segmenter import SentenceSegmenter

text = '据邯郸市教育局今日通报：为进一步落实《河北省教。题的补充。通知。》的文件精神，保证良好的教育发展环境，衡水市第一中学、衡水志臻中学、石家庄二中实验学校等16所外地民办普通高中学校因在邯郸市违规招生，被取消3年内的招生资格。'

# token_limits不为空时，加入长度限制；
# 例如token_limits=10时，每个分句的长度限制为10
ss = SentenceSegmenter(token_limits=None)

res = ss.segment(text)
for item in res:
    print(item)

```

## 中文分词

使用Jieba框架，可以自定义词典，停用词。



**分词使用方法**

```
from tokenizer import Tokenizer

tokenizer = Tokenizer(model_path=None)

line = '据邯郸市教育局今日通报'
for token in tokenizer.tokenize(line):
    print(token)
```

## 资源文件

- resources/stopwords.txt: 1,897个停用词 (来自多个开源项目的合并)
- resources/punctuation.txt: 71个标点符号

## 鸣谢


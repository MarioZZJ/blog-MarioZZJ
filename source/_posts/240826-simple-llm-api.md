---
title: 调用大模型 API 解决简单信息处理任务
comment: true
abbrlink: 573fc6c3
tags:
  - Python
  - LLM
categories: 工程实践
cover: 'https://download.mariozzj.cn/img/picgo/202409041617877.png'
date: 2024-08-26 18:00:00
updated:
keywords:
desc: 介绍如何调用大模型 API 解决简单信息处理任务
katex: false
aside: true
---

近年来大模型的发展推动了不同领域的范式变革，大模型强悍的理解、推理和生成能力使得其在各类任务中取得优异表现。
如今，随着大模型相关产业不断成熟，大模型的使用门槛也逐渐降低：不仅不具备大模型硬件本地部署条件的用户可以通过 API 获取大模型服务，且多家服务商提供了多样化的服务可供用户选择，调用 API 的成本也逐渐降低。
本文将以 [智谱 AI 开放平台](https://bigmodel.cn/) 为例，简要介绍如何调用大模型 API 解决简单信息处理任务，其他平台的使用方法可类推。
如需具体了解智谱 AI 开放平台的详细功能，可以参考 [智谱 AI 开放平台开发文档](https://bigmodel.cn/dev/howuse/)

# 开发平台准备

进入 [智谱 AI 开放平台](https://bigmodel.cn/)，注册账号。

![智谱 AI 网站界面](https://download.mariozzj.cn/img/picgo/202408261420667.png)

点击右上角的用户图标，进入个人中心，点击「实名认证」选项卡完成实名认证。

![控制台界面](https://download.mariozzj.cn/img/picgo/202408261424272.png)

![个人中心界面](https://download.mariozzj.cn/img/picgo/202408261426343.png)

![完成注册](https://download.mariozzj.cn/img/picgo/202408261426954.png)

随后点击右上角的「API 密钥」，创建一个 API 密钥。

![创建密钥](https://download.mariozzj.cn/img/picgo/202408261428481.png)

![点击「添加新的 API key」](https://download.mariozzj.cn/img/picgo/202408261433024.png)

![点击图标可复制密钥](https://download.mariozzj.cn/img/picgo/202408261434888.png)

点击复制即可复制密钥，这是调用 API 时服务端验证的凭证。

# 本地环境准备

在本地用于调用 API 的 Python 环境中，安装 `zhipuai` 包：

```bash
pip install zhipuai
```

安装完成后，可以参照 [接口文档](https://bigmodel.cn/dev/api) 中的 SDK 代码示例，执行一个简单的 API 调用，测试是否能够成功调用 API。

```python
from zhipuai import ZhipuAI
client = ZhipuAI(api_key="") # 填写您自己的APIKey
response = client.chat.completions.create(
    model="glm-4-0520",  # 填写需要调用的模型编码
    messages=[
        {"role": "user", "content": "作为一名营销专家，请为我的产品创作一个吸引人的slogan"},
        {"role": "assistant", "content": "当然，为了创作一个吸引人的slogan，请告诉我一些关于您产品的信息"},
        {"role": "user", "content": "智谱AI开放平台"},
        {"role": "assistant", "content": "智启未来，谱绘无限一智谱AI，让创新触手可及!"},
        {"role": "user", "content": "创造一个更精准、吸引人的slogan"}
    ],
)
print(response.choices[0].message)
```

这是一个要求智谱 AI 模型为用户创作 slogan 的例子，如果调用成功，将会输出智谱 AI 为用户创作的 slogan：

```
CompletionMessage(content='"智领变革，图创未来 —— 智谱AI，智慧加速每一步"', role='assistant', tool_calls=None)
```

至此，本地环境准备完成。

# 信息抽取任务应用

在线和本地环境准备完成后，我们就可以使用已有条件解决我们的实际问题了。事实上，我们在利用大模型 API 的理解与生成能力解决实际信息抽取任务中的问题时，都满足一个最基本的流程：

1. 构造有利于模型理解的输入。将待处理的**数据**和**要求**（体现为提示词 Prompt）转化为模型可理解的格式。这也是 *提示工程* 的重要一环。
2. 调用 API。将构造好的输入传入 API，调用模型进行处理，获取模型的输出。
3. 解析输出。将模型输出解析为我们需要的结果。为了提升解析的效率，我们可以在设计提示词时就考虑输出的格式，作为要求的一部分。

下面，我们以抽取主题词为例，展示如何使用大模型 API 解决简单信息抽取任务。

## 构造输入

我们构造的提示词输入是包含数据和要求的文本。设计提示词存在许多技巧，能够显著提升模型的输出效果，在这里不赘述具体技巧。我们这里直接使用智谱 AI 的 [Prompt 优化专家](https://chatglm.cn/share/FW4o9) 为我们的任务设计提示词：

> (我的输入) 我想要模型是一个能够理解学术论文文本内容的主题词提取专家。
> 我将向模型提供若干篇学术论文的标题和摘要内容，要求模型为每篇论文按照相关性依次输出和论文研究内容较为相关的五个主题词。
> 请在设计 prompt 时提供插入论文标题摘要的位置。输出要求仅包含关键词内容。
> 请使用代码块方式向我提供 prompt。

下面是优化专家提供的 prompt 模板：

```markdown

Role: Academic Paper Theme Extractor : 专注于从学术论文标题和摘要中提取主题词

## Goals
- 从提供的学术论文标题和摘要中提取五个与研究内容最相关的主题词。

## Constrains
- 输出必须仅包含关键词内容。
- 输出格式必须是Markdown列表形式。
- 按照相关性依次输出主题词。

## Skills
- 理解学术论文标题和摘要的能力。
- 提取关键信息的能力。
- 排序和筛选主题词的能力。

## Output Format
- 以Markdown列表形式输出五个主题词。

## Workflow
1. 读取并理解学术论文的标题和摘要。
2. 提取与研究内容最相关的主题词。
3. 按照相关性对主题词进行排序。
4. 以Markdown列表形式输出五个主题词。

## Initialization
你好，我是一个学术论文主题词提取专家。请提供学术论文的标题和摘要内容，我会从中提取五个与研究内容最相关的主题词，并以Markdown列表形式输出。

```

整理一下得到我们的 prompt：

```markdown
## Goals
- 从提供的学术论文标题和摘要中提取五个与研究内容最相关的主题词。

## Constrains
- 输出必须仅包含关键词内容。
- 输出格式必须是Markdown列表形式。
- 按照相关性依次输出主题词。

## Skills
- 理解学术论文标题和摘要的能力。
- 提取关键信息的能力。
- 排序和筛选主题词的能力。

## Output Format
- 以Markdown列表形式输出五个主题词。

## Workflow
1. 读取并理解学术论文的标题和摘要。
2. 提取与研究内容最相关的主题词。
3. 按照相关性对主题词进行排序。
4. 以Markdown列表形式输出五个主题词。

## 标题内容
{title}

## 摘要内容
{abstract}

```

我们在 prompt 中留下占位符，用于插入具体的学术论文标题和摘要。

## 批量调用 API

实际情况中，我们会有大批量的学术论文需要处理，我们可以使用批量处理的方法调用 API，节省成本的同时提高效率。

每个论文的处理可以视为一个对 API 的请求，我们把每个请求实例化为一个 json，预先生成所有请求的 json，一次性提交给 API。

在智谱 AI 的要求中，每个请求的 json 占据一行，至多 50000 个请求组成一个 `.jsonl` 文件，单个文件最大 100MB。其他平台要求可参见其文档要求。下面，我们假设我们已经读取了论文的标题、摘要，就可以生成一个待提交的 `.jsonl` 文件。

```python
content_template = """
## Goals
- 从提供的学术论文标题和摘要中提取五个与研究内容最相关的主题词。

## Constrains
- 输出必须仅包含关键词内容。
- 输出格式必须是Markdown列表形式。
- 按照相关性依次输出主题词。

## Skills
- 理解学术论文标题和摘要的能力。
- 提取关键信息的能力。
- 排序和筛选主题词的能力。

## Output Format
- 以Markdown列表形式输出五个主题词。

## Workflow
1. 读取并理解学术论文的标题和摘要。
2. 提取与研究内容最相关的主题词。
3. 按照相关性对主题词进行排序。
4. 以Markdown列表形式输出五个主题词。

## 标题内容
{title}

## 摘要内容
{abstract}
"""

# json 模版，注意这里可以指定特定模型
json_template = {"custom_id": "xxx", "method": "POST", "url": "/v4/chat/completions", "body": {"model": "glm-4", "messages": [{"role": "system", "content": "你是一个专注于从学术论文标题和摘要中提取主题词的专家"},{"role": "user", "content": "xxx"}]}}

for index, row in texts_df.iterrows():
    id = row["id"]
    title = row["title"]
    abstract = row["abstract"]
    # 将标题、摘要填入模板
    content = content_template.format(title=title, abstract=abstract)
    json_row = json_template.copy()
    json_row["custom_id"] = id
    json_row["body"]["messages"][1]["content"] = content

    # 写入文件一行
    with open(f"json_submit.jsonl", "a") as f:
        f.write(json.dumps(json_row, ensure_ascii=False) + "\n")
```

### 提交任务：通过 python
然后，我们可以把生成的 `.jsonl` 文件提交到文件系统：

```python

# 上传文件
from zhipuai import ZhipuAI
 
client = ZhipuAI(api_key="") # 填写自己的APIKey
  
result = client.files.create(
    file=open("json_submit.jsonl", "rb"),
    purpose="batch"
)
print(result.id)

```

```
1725432608_4dc1a45595254515ba04d446f78e383c
```

这段程序会打印一个文件 id，我们可以进一步将这个 id 提交给 API，用于创建任务。

```python
# 创建 batch 任务
from zhipuai import ZhipuAI
 
client = ZhipuAI(api_key="")  # 填写您自己的APIKey

create = client.batches.create(
    input_file_id="1725432608_4dc1a45595254515ba04d446f78e383c", # 填入文件 id
    endpoint="/v4/chat/completions", 
    completion_window="24h", #完成时间只支持 24 小时
    metadata={
        "description": "主题词抽取"
    }
)
print(create)
```
    
```
Batch(id='batch_1831223675418845184', completion_window='24h', created_at=xxxxxxxxx, endpoint='/v4/chat/completions', input_file_id='1725432608_4dc1a45595254515ba04d446f78e383c', object='batch', status='validating', cancelled_at=None, cancelling_at=None, completed_at=None, error_file_id=None, errors=None, expired_at=None, expires_at=None, failed_at=None, finalizing_at=None, in_progress_at=None, metadata={'description': '主题词抽取'}, output_file_id=None, request_counts=BatchRequestCounts(completed=None, failed=None, total=3))
```

这段程序会打印任务 id。服务端会需要一些时间处理任务，我们可以通过任务 id 查询任务的状态。

```python
batch_job = client.batches.retrieve("batch_1831223675418845184")
print(batch_job)
print(batch_job.output_file_id)
```

```
Batch(id='batch_1831223675418845184', completion_window='24h', created_at=0000000000000, endpoint='/v4/chat/completions', input_file_id='1725432608_4dc1a45595254515ba04d446f78e383c', object='batch', status='completed', cancelled_at=None, cancelling_at=None, completed_at=0000000000000, error_file_id='', errors=None, expired_at=None, expires_at=None, failed_at=None, finalizing_at=0000000000000, in_progress_at=0000000000000, metadata={'description': '主题词抽取'}, output_file_id='1725435402_e5bcfc2d00a34ce4a0c9ea58c27b1041', request_counts=BatchRequestCounts(completed=3, failed=0, total=3))
1725435402_e5bcfc2d00a34ce4a0c9ea58c27b1041
```

当任务完成后，我们可以通过 `output_file_id` 下载结果文件。

```python
from zhipuai import ZhipuAI
 
client = ZhipuAI(api_key="")  # 填写自己的APIKey
content = client.files.content("1725435402_e5bcfc2d00a34ce4a0c9ea58c27b1041") 

# 使用write_to_file方法把返回结果写入文件
content.write_to_file("write_to_file_batchoutput.jsonl")
```

### 提交任务：通过控制台网页

我们也可以通过控制台网页提交任务。在 [BigModel 控制台 - Batch 数据](https://bigmodel.cn/console/batch/dataset) 页面，点击「上传数据」，上传我们生成的 `.jsonl` 文件。

![上传数据](https://download.mariozzj.cn/img/picgo/202409041546267.png)

接着，在 [BigModel 控制台 - Batch 任务](https://bigmodel.cn/console/batch/task) 界面可以查看任务状态。

![任务状态](https://download.mariozzj.cn/img/picgo/202409041548999.png)

如果任务已完成，则可以在状态详情页下载成功文件。

![](https://download.mariozzj.cn/img/picgo/202409041549490.png)

## 解析输出

我们可以通过解析输出文件，将模型输出的结果解析为我们需要的格式。输出文件同样是一个 `.jsonl` 文件，每行是一个请求的结果。形如

```
{"response":{"status_code":200,"body":{"created":1725435303,"usage":{"completion_tokens":38,"prompt_tokens":373,"total_tokens":411},"model":"glm-4","id":"8991732081299811096","choices":[{"finish_reason":"stop","index":0,"message":{"role":"assistant","content":"- 颠覆性技术识别\n- 社会网络分析\n- 突变理论\n- 技术共现网络\n- 语义信息分析"}}],"request_id":"test103"}},"custom_id":"test103","id":"batch_1831223675418845184"}
```
我们需要的是 `choices` 字段中的 `message` 字段中的 `content`。

```python
ids = []
results = []

with open("write_to_file_batchoutput.jsonl") as f:
    for line in f:
        response = json.loads(line)["response"]["body"]
        id = response['request_id']
        content = response['choices'][0]['message']['content'].replace('- ','').split('\n')
        print(f"{id}:  {content}")
        ids.append(id)
        results.append(content)
```

```
ids = []
results = []

with open("write_to_file_batchoutput.jsonl") as f:
    for line in f:
        response = json.loads(line)["response"]["body"]
        id = response['request_id']
        content = response['choices'][0]['message']['content'].replace('- ','').split('\n')
        print(f"{id}:  {content}")
        ids.append(id)
        results.append(content)
```

这样，我们就通过调用大模型 API 完成了一个简单的关键词抽取任务。事实上，我们可以通过类似的方法解决更多的信息处理任务，如文本分类、文本生成等，只需要根据具体任务的要求设计好提示词，调用 API，解析输出即可。
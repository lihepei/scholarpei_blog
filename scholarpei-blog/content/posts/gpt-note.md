+++
title = "GPT Prompt学习笔记-以Learning Prompt为教程" 
date = 2024-03-18T23:16:55+08:00
draft = false
hidden = false
tag = ["note","gpt","Chatgpt"]
slug = 'Gpt Note'

+++

本笔记学习来自于网站 https://learningprompt.wiki/zh-Hans/
推荐大家学习，很有帮助

# To Do and Not to Do

在进行一个空泛的提问时，如推荐一部电影`Recommend a movie to me`，ai往往会问你喜欢什么类型的电影，这样要聊很多轮，效率很低

所以可以添加下面的语句到prompt中：

`DO NOT ASK FOR INTERESTS. DO NOT ASK FOR PERSONAL INFORMATION.`

OpenAI的API最佳实践文档写道：

> **Instead of just saying what not to do, say what to do instead. 与其告知模型不能干什么，不妨告诉模型能干什么。**

尽管ai理解Not to do，但是加入更多限定词，告知模型干什么，回答效率更高更准确，如果还是推荐电影这个例子，可以这样写：

`Recommend a movie from the top global trending movies to me.`

![](https://github.com/lihepei/scholarpei_blog/blob/main/scholarpei-blog/static/blog-picture/gpt-note/1.png?raw=true)

# 使用引导词，引导模型输出特定内容

在prompt的最后加上你希望ai给出的答复的首个单词的引导词，在要求ai写代码的时候作用甚好，比如写一个SQL的代码，下面会是更好的prompt

```
Create a MySQL query for all students in the Computer Science Department:
Table departments, columns = [DepartmentId, DepartmentName]
Table students, columns = [DepartmentId, StudentId, StudentName]
SELECT
```

在末尾加上SELECT提示ai用SQL代码输出

同理，python语言可以用`import`作为引导词

在吴恩达 prompt engineering 课程中，引导词是在中间出现的，不同的是，它是指定让gpt以指定形式输出

```
prompt = f"""
Generate a list of three made-up book titles along \
with their authors and genres.
Provide them in JSON format with the following keys:
book_id, title, author, genre.
"""
```

可以看到中间要求以JSON格式输出

# 增加role以符合需求

想把邮件更加商务，就加入business相关的词

让 AI 假设自己是一个小学老师，并且很擅长将复杂的内容转变成 7、8 岁小朋友也能听懂的话，然后再改写这段话：

```
You are a primary school teacher who can explain complex content to a level that a 7 or 8 year old child can understand. Please rewrite the following sentences to make them easier to understand:
```

输出就会更加易懂

除了添加角色还可以让AI模拟某个人，比如让 AI 以 David Foster Wallace 的风格改写上述那段话：

`Imagine you are the famous writer David Foster Wallace, please rewrite this sentences.`

# 使用特殊符号指令和需要处理的文本分开

可以用 “”“将指令和文本分开。根据我的测试，如果你的文本有多段，增加”“” 会提升 AI 反馈的准确性（这个技巧来自于 OpenAI 的 API [最佳实践文档](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-openai-api)）

这个的原因是因为AI不知道哪部分是指令，哪部分是文本，我们手动指定标出，就会提高准确性。

```
Please summarize the following sentences to make them easier to understand.
OpenAI is an American artificial intelligence (AI) research laboratory consisting of the non-profit OpenAI Incorporated (OpenAI Inc.) and its for-profit subsidiary corporation OpenAI Limited Partnership (OpenAI LP). OpenAI conducts AI research with the declared intention of promoting and developing a friendly AI. OpenAI systems run on the fifth most powerful supercomputer in the world.[5][6][7] The organization was founded in San Francisco in 2015 by Sam Altman, Reid Hoffman, Jessica Livingston, Elon Musk, Ilya Sutskever, Peter Thiel and others,[8][1][9] who collectively pledged US$1 billion. Musk resigned from the board in 2018 but remained a donor. Microsoft provided OpenAI LP with a $1 billion investment in 2019 and a second multi-year investment in January 2023, reported to be $10 billion.[10]
```

下面是更好的Prompt

```
Please summarize the following sentences to make them easier to understand.

Text: """
OpenAI is an American artificial intelligence (AI) research laboratory consisting of the non-profit OpenAI Incorporated (OpenAI Inc.) and its for-profit subsidiary corporation OpenAI Limited Partnership (OpenAI LP). OpenAI conducts AI research with the declared intention of promoting and developing a friendly AI. OpenAI systems run on the fifth most powerful supercomputer in the world.[5][6][7] The organization was founded in San Francisco in 2015 by Sam Altman, Reid Hoffman, Jessica Livingston, Elon Musk, Ilya Sutskever, Peter Thiel and others,[8][1][9] who collectively pledged US$1 billion. Musk resigned from the board in 2018 but remained a donor. Microsoft provided OpenAI LP with a $1 billion investment in 2019 and a second multi-year investment in January 2023, reported to be $10 billion.[10]
"""
```

# 通过格式词阐述需要输出的格式

手动告诉ai需要输出的格式形式，常用于生成文本场景。

例子：按topic总结，每个topic按无序列表罗列topic中的观点

```
Summarize the main points of the following speech
Use the following format:
Topic 1: <topic_name_1>
- <point_1>
..
Topic 2: <topic_name_2>
- <point_1>
..
Topic 10: ..

Text: """
Thank you so much, Fred, for that lovely introduction. And thanks to the Atlantic Council for hosting me today.

The course of the global economy over the past two years has been shaped by COVID-19 and our efforts to fight the pandemic. It’s now evident, though, that the war between Russia and Ukraine has redrawn the contours of the world economic outlook. Vladimir Putin’s unprovoked attack on Ukraine and its people is taking a devastating human toll, with lives tragically lost, families internally displaced or becoming refugees, and communities and cities destroyed.
...

"""
```

output会是：

```
Topic 1: The war in Ukraine
- The war is taking a human toll with lives lost, families displaced, and communities destroyed
- The Biden administration is committed to holding Russia accountable
- The war has violated international law and is a challenge to the international order

Topic 2: The global economy
- The war is having negative impacts on the global economy, including higher commodity prices and inflation
- The IMF and World Bank will be focused on helping developing countries weather the impacts of the war
- The ultimate outcome for the global economy depends on the path of the war
```

此技巧可以使输出更加结构化，方便你下一步进行自动化

# Zero-Shot Prompts

使用一个叫做Chain of thought技巧

在问题的结尾加上：`Let's work this out in a step by step way to be sure we have the right answer.` （让我们逐步思考），模型输出的答案会更加准确。

解释：

1. 首先各位要清楚像 ChatGPT 这类产品，它是一个统计语言模型，本质上是基于过去看到过的所有数据，用统计学意义上的预测结果进行下一步的输出（这也就是为什么你在使用 ChatGPT 的时候，它的答案是一个字一个字地吐出来，而不是直接给你的原因，因为答案是一个字一个字算出来的）。
2. 当它拿到的数据里有逻辑，它就会通过统计学的方法将这些逻辑找出来，并将这些逻辑呈现给你，让你感觉到它的回答很有逻辑。
3. 在计算的过程中，模型会进行很多假设运算（不过暂时不知道它是怎么算的）。比如解决某个问题是从 A 到 B 再到 C，中间有很多假设。
4. 它第一次算出来的答案错误的原因，只是因为它在中间跳过了一些步骤（B）。而让模型一步步地思考，则有助于其按照完整的逻辑链（A > B > C）去运算，而不会跳过某些假设，最后算出正确的答案。

![ZeroShotChainOfThought002png](https://learningprompt.wiki/zh-Hans/assets/images/ZeroShotChainOfThought002-8036bee6fdf39bd866804837c013319a.png)

这个技巧除了用于**解决复杂问题**之外，还适合**生成一些连贯主题的内容**（长篇文章、电影剧本）

# Few-Shot Prompting

请注意：思维链仅在使用**大于等于100B参数**的模型时，才会生效

我们可以给模型一些示例，从而让模型返回更符合我们需求的答案

![](https://github.com/lihepei/scholarpei_blog/blob/main/scholarpei-blog/static/blog-picture/gpt-note/2.png?raw=true)

这种示例给出让gpt明白你想干什么，希望得到什么结果（让它更好去贴切下一个接的“话”）

![](https://github.com/lihepei/scholarpei_blog/blob/main/scholarpei-blog/static/blog-picture/gpt-note/3.png?raw=true)

解决办法：

## Few-Shot Chain of Thought

> 通过向大语言模型展示一些少量的样例，并在样例中解释推理过程，大语言模型在回答提示时也会显示推理过程。这种推理的解释往往会引导出更准确的结果。

![FewShotChainOfThought001png](https://learningprompt.wiki/zh-Hans/assets/images/FewShotChainOfThought001-cb0c8729dadd349fa916ca44c1c18404.png)

**将推导的逻辑过程加入到示例的编写中**

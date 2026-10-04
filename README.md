修改了来自 MARKTECH 的内容

只改了模型这一处，在 agent.py 的 main 函数里（约第 219 行）：

```python
# 原版
agent = ReActAgent(tools=tools, model="openai/gpt-4o", project_directory=project_dir)

# 现在
agent = ReActAgent(tools=tools, model="deepseek/deepseek-v4-flash", project_directory=project_dir)
```

除这一行之外，其余代码与原始版本完全一致。

之前尝试过的改动都已去掉：

- 文件工具的路径安全限制
- system prompt 末尾的目录约束
- temperature=0 / max_tokens=3000

注意：deepseek/deepseek-v4-flash 通过 OpenRouter 调用，没有免费版，需要账户有可用额度。

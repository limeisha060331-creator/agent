修改了来自MARKTECH的内容

1. 给文件工具加了路径安全限制
在 ReAct 循环里，执行工具前先判断：如果工具是 read_file 或 write_to_file，就把目标路径解析成绝对路径，检查它是否在项目目录内。不在的话就不执行，而是返回一条“路径必须在项目目录内”的错误提示，让模型重新规划。
if tool_name in {"read_file", "write_to_file"} and args:
    requested_path = os.path.abspath(args[0])
    allowed_root = os.path.abspath(self.project_directory)
    ...
    if not is_in_project:
        observation = f"工具执行错误：文件路径必须位于项目目录 {allowed_root} 内……"

2. 在 system prompt 末尾追加了目录约束（约 102 行处）
原本只是直接返回渲染好的模板，改成先渲染再拼接一段提示，明确告诉模型本次项目目录是哪个，并要求 read_file / write_to_file 的路径必须在该目录内（举例说在该目录里创建 index.html、style.css、script.js，不要用 C:\snake_game）。

3. 调整了模型调用参数（约 128 行处）
给 chat.completions.create 加了：
temperature=0,
max_tokens=3000,
（为了配合 OpenRouter 的额度上限）
整体目的很明确：防止模型把文件写到项目目录之外，同时固定采样温度、限制输出长度来适配免费额度。

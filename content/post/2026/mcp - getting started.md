+++
author = "Binwei Wu"
title = "MCP - Getting Started"
date = "2026-03-02"
description = "MCP - Getting Started"
featured = true
tags = [
    "ai"
]
categories = [
    "Engineering",
]
series = "2026"
aliases = ["migrate-from-jekyl"]

+++

This is a MCP Getting Started, to set up the simplest mcp server and go through the simple scenario.

```
mkdir my-mcp-server
cd my-mcp-server

python -m venv venv
source venv/bin/activate

pip install fastmcp
pip install pytz
```

Create server.py

```
from fastmcp import FastMCP

mcp = FastMCP("我的第一个MCP服务器")

@mcp.tool
def greet(name: str) -> str:
    return f"你好，{name}！欢迎使用MCP服务器。"

@mcp.tool
def add_numbers(a: int, b: int) -> int:
    return a + b

@mcp.tool
def get_current_time(timezone: str = "Asia/Shanghai") -> str:
    from datetime import datetime
    import pytz
    tz = pytz.timezone(timezone)
    return datetime.now(tz).strftime("%Y-%m-%d %H:%M:%S")

if __name__ == "__main__":
    mcp.run(transport="stdio")

```

Create test_client.py

```
# test_client.py
import asyncio
from fastmcp import Client

async def main():
    async with Client("server.py") as client:
        tools = await client.list_tools()
        print("可用工具：", [t.name for t in tools])

        result = await client.call_tool("greet", {"name": "小明"})
        print(result.content[0].text)

asyncio.run(main())
```

There is no need to launch the server, which is handy for debugging purpose. You can also config in the Claude Code to connect the mcp through python + file name. This way is not suitable in prod environment since the file cannot serve multiple clients, mainly we need to lauch the mcp server.

Update server.py v2

```
...
if __name__ == "__main__":
    mcp.run(
        transport="streamable-http",
        host="127.0.0.1",
        port=8000,
        path="/my_test_mcp",
    )
```

Update test_client.py v2

```
...
async with Client("http://127.0.0.1:8000/my_test_mcp") as client:
...
```

Optional: can also install MCP Inspector for debugging

```
npx -y @modelcontextprotocol/inspector
```

Connect Claude Code to MCP
Open ~\.claude.json
Insert content
```
      "mcpServers": {
        "mymcphttpserver": {
          "type": "http",
          "url": "http://127.0.0.1:8000/my_test_mcp"
        }
      },
```

Then open claude code, type /mcp you can see the mcp and its tools. Try to input "greet miao", the relevant greet tool from the mcp server will get triggered and return the result.

*Written by Binwei@Suzhou*
# 前提准备

- 搭建一个可以运行的koshi服务器
- 拥有一个openai的账号 `[https://platform.openai.com/account/api-keys](https://platform.openai.com/account/api-keys)`
- 拥有一台服务器并拥有python环境（如果不需要在服务器在搭建可以不需要）

# 一、本地自建server

- 本地需要一个koishi的服务器

进入到官方插件的地址 @42

[https://github.com/MirrorCY/openchat](https://github.com/MirrorCY/openchat)

在我们的项目中新建一个server目录，然后新建一个 `chat.py`文件 ，再新建一个 `requirements.txt`

```python
# chat.py
from revChatGPT.Official import Chatbot
from fastapi import FastAPI
import uvicorn
from pydantic import BaseModel

port = 8006
app = FastAPI()
chatbot = Chatbot(api_key="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")

# 只需填入你的 openai api_key 即可。一键直达 https://platform.openai.com/account/api-keys
# 不再需要 2captcha 密钥
# 按照 https://github.com/acheong08/ChatGPT 的说法是完全免费的，但未经证实

class ChatRequest(BaseModel):
  prompt: str

@app.get("/ping")
def ping():
  return {"message": "pong"}

@app.post("/chat")
def chatGPT(request: ChatRequest):
  prompt = request.prompt
  print(prompt)
  if prompt == "__clear__":
    chatbot.reset()
    return {"message": "OK"}
  answer = chatbot.ask(prompt)["choices"][0]["text"]
  print(answer)
  return {"message": answer}

if __name__ == "__main__":
  uvicorn.run(app, host="0.0.0.0", port=port)
```

requriements.txt

revChatGPT不要用原来的`1.0.2`，要用最新的`1.1.4`

```python
fastapi~=0.89.1
pydantic~=1.10.2
revChatGPT~=1.1.4
uvicorn~=0.20.0
```

新建一个Python运行环境 venv

```python
virutalenv venv
souce venv/bin/activate
pip install -r requirments.txt
python chat.py
```


# 二、服务器自建server


```python
# 安装依赖
sudo apt update 
sudo apt ugrade 
sudo apt install git -y
pip3 install virtualenv

# 下载项目源码
git clone https://github.com/houko/koishi-openchat-server.git

# 进入项目
cd koishi-openchat-server

# 创建虚拟环境
virtualenv .venv

# 进入虚拟环境
source .venv/bin/activate

# 安装依赖
pip3 install -r requirements.txt

vim chat.py
# 修改第8行代码，把api_key换成自己的
# chatbot = Chatbot(api_key="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")

# 运行服务器
python3 chat.py

```

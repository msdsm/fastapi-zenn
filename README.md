# FastAPI入門
https://zenn.dev/sh0nk/books/537bb028709ab9/viewer/bdf8a5


## メモ

### FastAPI概要
- 自動的にSwagger UIのドキュメントが生成される
- スキーマ駆動開発
- ASGIに対応していて非同期処理ができて高速
- 基本的な書き方は以下
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/hello")
async def hello():
    return {"message": "hello world!"}
```
- appはFastAPI()インスタンス
- このときlocalhost:8000/helloでこのAPIがたたかれる
- localhost:8000/docsにswagger UI
  - swagger UI上でAPIたたくこともできる
### パスオペレーション関数
- 以下のようにFastAPIインスタンスに対するデコレータで修飾された関数のこと
```python
@app.get("/hello")
async def hello():
    return {"message": "hello world!"}
```
- パスは/helloの部分
- オペレーションはgetの部分, つまりHTTPメソッドのこと
### `__init__.py`
- https://qiita.com/msi/items/d91ea3900373ff8b09d7
### uvicorn
### ASGI
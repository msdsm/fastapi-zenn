# FastAPI入門

## ソース
- https://zenn.dev/sh0nk/books/537bb028709ab9


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



### Optional
- Noneを返す場合のある関数の型ヒントを書くのに使う
```python
from pathlib import Path
from typing import Optional

def get_latest_log_path(search_dir: Path) -> Optional[Path]:
```
- Python3.10以降では以下のようにかける
```python
from pathlib import Path

def get_latest_log_path(search_dir: Path) -> Path | None:
```

### レスポンス型の定義
- pydanticを使って以下のように定義できる
```python
from pydantic import BaseModel, Field

class Task(BaseModel):
    id: int
    title: Optional[str] = Field(None, example="クリーニングを取りに行く")
    done: bool = Field(False, description="完了フラグ")
```
- `BaseModel`を継承する形でスキーマモデルを定義
- 各フィールド`id`,`title`,`done`に対して`int`,`Optional[str]`,`bool`の型ヒントを付加している
- `Field`はフィールドに関する付加情報を記述
  - 第一引数はデフォルト値
  - `example`は値の例
  - `description`は説明
  - `example`, `description`で指定したものがSwaggerで表示される
![schemas](./images/schemas.png)


- この型を使用してAPIのレスポンスを以下のように定義できる
```python
from typing import List
@router.get("/tasks", response_model=List[task_schema.Task])
async def list_tasks():
  return [task_schema.Task(id=1, title="1つ目のTODOタスク")]
```
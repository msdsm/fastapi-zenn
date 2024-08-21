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

### リクエスト型の定義
- POST /taskでタスクを新規作成する際にリクエストボディでtitleのみを指定する
- このとき、POST /taskのリクエストボディとGET /taskのレスポンスボディでtitleが共通なので以下のようにかく
```python
from typing import Optional

from pydantic import BaseModel, Field

class TaskBase(BaseModel):
    title: Optional[str] = Field(None, example="クリーニングを取りに行く")

class TaskCreate(TaskBase):
    pass

class TaskCreateResponse(TaskCreate):
    id: int

    class Config:
        orm_model = True

class Task(TaskBase):
    id: int
    done: bool = Field(False, description="完了フラグ")

    class Config:
        orm_mode = True
```
- このようにBaseModelを継承する形で共通フィールドをTaskBaseというクラスに持たせる
- このTaskBaseを継承する形で、POST /taskのリクエストとレスポンス、GET /taskのレスポンスを作成する
- APIでリクエストボディの型は以下のようにかく
  - レスポンスはデコレータを使ってrouterの引数
  - リクエストは関数の引数で型ヒント
```python
@router.post("/tasks", response_model=task_schema.TaskCreateResponse)
async def create_task(task_body: task_schema.TaskCreate):
    return task_schema.TaskCreateResponse(id=1, **task_body.dict())
```
- また、リクエストパラメータは`{}`をパスに埋め込んでその変数を引数として与える
```python
@router.delete("/tasks/{task_id}", response_model=None)
async def delete_task(task_id: int):
    return
```


### キーワード引数とdict
- `dict`インスタンスに対して先頭に`**`をつけることで`dict`をキーワード引数として展開できる
```python
task_schema.TaskCreateResponse(id=1, **task_body.dict())
```
- `task_schema.TaskCreateResponse(id=1, title=task_body.title, done=task_body.done)`と等価
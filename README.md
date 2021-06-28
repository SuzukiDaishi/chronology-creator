# chronology-creator
年表作成ツールの作成です。

## なにこれ
本の年表をJSONに変更する作業を手助けするツールです。

## JSON形式
```json=
{
    "title": タイトル(string),
    "description": Markdownでの説明(string),
    "chronology": [
        {
            "year": 西暦(number),
            "text": その年になにがあったかのMarkdown(string)
        },
        ...
    ]
}
```

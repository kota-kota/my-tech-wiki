---
title: Mkdocsプラグインの記述テスト
---

## pymdownx.keys

- コード

```text
++alt+shift+space++
```

- 表示結果

++alt+shift+space++

## pymdownx.mark

- コード

```text
文字の背景色を ==こんな感じ== にできる
```

- 表示結果

文字の背景色を ==こんな感じ== にできる

## admonition

- コード

```text
!!! note "Title note"
    hogehoge

!!! attention
    attention!!
```

- 表示結果

!!! note "Title note"
    hogehoge

!!! attention
    attention!!

## pymdownx.highlight

- 表示結果

```bash
pip install mkdocs
```

```cpp title="main.cpp" linenums="5"
#include <iostream>
void main() {
    std::cout << "Hello world!" << std::endl;
}
```

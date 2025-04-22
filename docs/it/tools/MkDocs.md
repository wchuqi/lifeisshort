# Welcome to MkDocs
For full documentation visit [mkdocs.org](https://www.mkdocs.org).

## 安装
```shell
pip install mkdocs

mkdocs --version
python -m mkdocs --version

mkdocs serve
http://127.0.0.1:8000
```

## Commands
* `mkdocs new [dir-name]` - Create a new project.
* `mkdocs serve` - Start the live-reloading docs server.
* `mkdocs build` - Build the documentation site.
* `mkdocs -h` - Print help message and exit.

## Project layout
```text
mkdocs.yml    # The configuration file.
docs/
    index.md  # The documentation homepage.
    ...       # Other markdown pages, images and other files.
```

## 主题
下载 material 主题：`pip install mkdocs-material`

[源码](https://github.com/squidfunk/mkdocs-material)


## 参考
[Material for MkDocs 中文文档](https://mkdoc-material.llango.com/setup/setting-up-navigation/)

[Material 主题设置](https://yiruru.com/6%E4%BB%A3%E7%A0%81%E5%A6%82%E8%AF%97/Material%E4%B8%BB%E9%A2%98%E8%AE%BE%E7%BD%AE/)

[快来美化你的MKDocs吧](https://juejin.cn/post/7066641709198737416)

[so easy！从头教你用mkdocs构建个人博客系统](https://blog.csdn.net/qq_41261251/article/details/116021097)

[笔记文档一把梭——MkDocs 快速上手指南](https://sspai.com/prime/story/mkdocs-primer)

## 小技巧
支持代码标题：
```python title='demo.py'
def sayhi():
    return "hi,Python全栈开发"
```


# 注释的外观

```json
    "doxdocgen.c.triggerSequence": "/**",
    "doxdocgen.c.commentPrefix": " * ",
    "doxdocgen.c.firstLine": "/**",
    "doxdocgen.c.lastLine": " */",
```

# 注释关键字

## 通用关键字

```json
    "doxdocgen.generic.order": [
        "brief",
        "empty",
        "param",
        "return",
    ],
    "doxdocgen.generic.authorEmail": "roboxs@163.com",
    "doxdocgen.generic.authorName": "roboxs",
    "doxdocgen.generic.authorTag": "@author {author} ({email})",
    "doxdocgen.generic.dateFormat": "YYYY.MM.DD",
    "doxdocgen.generic.dateTemplate": "@date {date}",
    "doxdocgen.generic.paramTemplate": "@param[in/out] {param} ",
    "doxdocgen.generic.returnTemplate": "@return {type} ",
```

## 文件关键字

```json
"doxdocgen.file.fileOrder": [
    "file",
    "author",
    "brief",
    "version",
    "date",
    "empty",
    "copyright",
    "empty",
    "custom"
],
"doxdocgen.file.fileTemplate": "@file {name}",
"doxdocgen.file.versionTag": "@version 1.0.0",
"doxdocgen.file.copyrightTag": [
    "@copyright Copyright (c) {year}"
],
"doxdocgen.file.customTag": [
    "todo todo"
],
```
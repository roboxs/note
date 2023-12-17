# 注释的外观

+ 第一行

  ```json
  "doxdocgen.c.firstLine": "/*******************************************************************************",
  ```

+ 最后一行

  ```json
  "doxdocgen.c.lastLine": " ******************************************************************************/"
  ```

+ 每一行

# 注释关键字

## 通用关键字

```json
"doxdocgen.generic.authorEmail": "roboxs@163.com",
"doxdocgen.generic.authorName": "roboxs",
"doxdocgen.generic.authorTag": "@author {author} ({email})",
"doxdocgen.generic.dateFormat": "YYYY.MM.DD",
"doxdocgen.generic.dateTemplate": "@date {date}",
```

## 文件关键字

```json
/*doxygen*/
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




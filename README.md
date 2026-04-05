<!---
  Licensed to the Apache Software Foundation (ASF) under one
  or more contributor license agreements.  See the NOTICE file
  distributed with this work for additional information
  regarding copyright ownership.  The ASF licenses this file
  to you under the Apache License, Version 2.0 (the
  "License"); you may not use this file except in compliance
  with the License.  You may obtain a copy of the License at

  http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing,
  software distributed under the License is distributed on an
  "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
  KIND, either express or implied.  See the License for the
  specific language governing permissions and limitations
  under the License. -->
# Apache Hive Documentation Site

This repository contains the code for generating the Apache Hive web site.
It's built with Hugo and hosted at https://hive.apache.org.

### Build and Run Locally

* Clone this repository.
* Install [hugo] on macOS:

 ```brew install hugo```
* For other OS please refer: [hugo-install] 
* To verify your new install:

```hugo version```

* To build and start the Hugo server run:
```
>>> hugo server -D

                   | EN
+------------------+----+
  Pages            | 10
  Paginator pages  |  0
  Non-page files   |  0
  Static files     |  3
  Processed images |  0
  Aliases          |  1
  Sitemaps         |  1
  Cleaned          |  0

Total in 11 ms
Watching for changes in /Users/bep/quickstart/{content,data,layouts,static,themes}
Watching for config changes in /Users/bep/quickstart/config.toml
Environment: "development"
Serving pages from memory
Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop
```
* Navigate to `http://localhost:1313/` to view the site locally.


### To Add New Content 

* To add new markdown file : 
`hugo new general/Downloads.md`

* Update `themes/hive/layouts/partials/menu.html` and `config.toml` to add navigation link to the markdown page as needed.

### Pushing to site
Commit and push the changes to the main branch. The site is automatically deployed from the site directory.

# Fix

### Hugo 的时区处理机制导致的”时差”问题

#### 方案一：强制开启“未来日期渲染”（最快解决）

在启动命令中加入 `-F` 参数（`F` 代表 `Future`）：

```bash
hugo server -D -F
```

#### 方案二：**在配置文件里全局定调**

如果你不想每个文件都写那么长，在你的 `hugo.toml` (或 `config.toml`) 里加上这一行，让 Hugo 默认跟着你的系统时区走：

```bash
timezone = "Asia/Shanghai"
```

[hugo]: https://gohugo.io/getting-started/quick-start/
[hugo-install]: https://gohugo.io/installation/
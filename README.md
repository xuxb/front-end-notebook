# 项目说明

### 本地运行
1. `npm install hexo-cli -g`

### 使用 hexo 创建项目
* 全局安装 hexo，执行命令 `npm install hexo-cli -g`

* 初始化项目，执行命令 `hexo init blog`，会生成以下目录结构

```
.
├── _config.yml    # 网站的配置信息
├── package.json   # 应用程序的信息
├── public         # 公共文件夹，用于存放生成的站点文件
├── scaffolds      # 模版文件夹
├── source         # 资源文件夹，用来存放内容
└── themes         # 主题文件夹
```
* 安装依赖 `npm i`
* 添加 md 文件至 `source/_posts`，`hexo new fileName`

生成静态文件：`hexo generate` 或者 `hexo g`
启动本地服务：`hexo server` 或者 `hexo s`
清除缓存文件: `hexo clean`
部署网站：`hexo deploy` 或者 `hexo d`


**发布到 gitHub**
1. 执行 `npm i hexo-deployer-git -D`
2. 修改 `_config.yml` 文件，设置 `deploy` 字段的 `repo` 、`branch` 字段
3. 执行 `hexo g` 生成静态文件
4. 执行 `hexo d` 发布到指定仓库

**添加主题**
1. 在根目录执行 `git clone https://github.com/cofess/hexo-theme-pure.git themes/pure`
2. 操作成功后会在 themes 目录生成 pure 文件夹
3. 修改 `_config.yml` 文件，设置 `theme: pure`
4. 执行 `hexo g` 生成静态文件
5. 执行 `hexo s` 预览页面

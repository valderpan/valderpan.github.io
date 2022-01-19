# 使用Hugo搭建个人博客步骤


# 使用Hugo搭建个人博客步骤：

1.安装Hugo、Git等必要软件

2.执行 `hugo new site MyBlog`创建本地博客文件夹

3.执行`git init`将文件夹交给git管理

4.执行`git clone https://github.com/dillonzq/LoveIt.git themes/LoveIt`指令下载主题

5.修改博客根目录下的`config.toml`文件

6.执行`hugo new posts/my-first-post.md`添加第一篇博客

7.执行`hugo server -D -e production`在本地预览博客

8.Github创建对应的仓库，注意仓库名

9.执行`hugo -D`编译静态博客页面

10.执行`git init`将`public`文件夹交给git管理

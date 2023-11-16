---
title: 记录我如何通过 jekyll 和 github pages 部署个人博客
date: 2023-11-13 21:00:00 +0800
categories: [others]
tags: [github]
pin: false
---

## 准备动作

需要先准备好的有 github 账号、安装 git、安装 ruby 和 jekyll。

因为前两个我之前就已经安装好了，所以实际上只做了 ruby 和 jekyll 的安装。

### 安装 ruby 和 jekyll

参考了[官方文档](https://jekyllrb.com/docs/installation/macos/)，我是 MacOS 而且之前已经安装过 Homebrew，所以只需要通过 brew 安装好 ruby，再安装 jekyll 就可以了。

安装 ruby 指令如下。

```bash
brew install chruby ruby-install xz
ruby-install ruby 3.1.3
echo "source $(brew --prefix)/opt/chruby/share/chruby/chruby.sh" >> ~/.zshrc
echo "source $(brew --prefix)/opt/chruby/share/chruby/auto.sh" >> ~/.zshrc
echo "chruby ruby-3.1.3" >> ~/.zshrc # run 'chruby' to see actual version
# 待上述完成后，因为 mac 本身也会自带 ruby，所以需要特别看一下 ruby 版本确认是否安装成功，
ruby -v
```

安装 jekyll 指令如下。

```bash
gem install jekyll
```

我在帮朋友安装的时候，因为网络环境问题上述指令会出现问题，需要配置好 brew 的国内源。

## 选择适合的主题

时至今日，许多伟大的开发者贡献出了风格各异的主题，在搜索框输入 jekyll themes 就可以看到各种各样的主题。

选择的时候注意以下几点：

1. 这个主题的功能是否满足需求？评论区、全局搜索、深浅切换……这些功能虽然常见，但并不总是都有。
2. 这个主题呈现的风格是否满足要求？比如你的博客主要用来放图片，那么就可以选择文字占比比较小的主题；如果用来写文档，就要选择文字占比多的主题。
3. 这个主题的文档说明是否清晰完整？虽然每个主题配置过程都大同小异，但是文档越完整，踩坑的几率就越小嘛。

我选择的主题是 [Chirpy](https://chirpy.cotes.page/)。

## 创建本地仓库开始自定义

### 使用他人模板

根据 [Chirpy 的官方文档](https://chirpy.cotes.page/posts/getting-started/)，需要先在 github 中对 https://github.com/cotes2020/chirpy-starter 选择 Use this template 来创建一个新仓库，并将这个仓库命名为 `username.github.io` 。username 指的是 github 中的 username。

别的主题方法稍有差别，具体看文档。fork 仓库也是常见的方法。

### clone 到本地

此时，我已经拥有了一个自己的仓库，我将这个仓库 clone 到了本地。在本地仓库的根目录下，打开 terminal 运行：

```bash
$ bundle
```

就可以在本地运行了。

### 修改 config 进行个性化

打开 clone 下来的文件夹，找到名为 `_config.yml` 的文件，进行自定义设置。每个主题的 config 都会大同小异，最关键的一项是 `url` ，填入仓库名称，也就是 `username.github.io` 。其他的按照文件上的说明填写即可。

对于 Chirpy 主题，可以定义评论功能。

```yaml
comments:
  active: # The global switch for posts comments, e.g., 'disqus'.  Keep it empty means disable
  # The active options are as follows:
  disqus:
    shortname: # fill with the Disqus shortname. › https://help.disqus.com/en/articles/1717111-what-s-a-shortname
  # utterances settings › https://utteranc.es/
  utterances:
    repo: # <gh-username>/<repo>
    issue_term: # < url | pathname | title | ...>
  # Giscus options › https://giscus.app
  giscus:
    repo: # <gh-username>/<repo>
    repo_id:
    category:
    category_id:
    mapping: # optional, default to 'pathname'
    input_position: # optional, default to 'bottom'
    lang: # optional, default to the value of `site.lang`
    reactions_enabled: # optional, default to the value of `1`
```

有 disqus, utterances, giscus 三种方式可选。我选择的是 [giscus](https://giscus.app/zh-CN)，通过网站上的指引获取到 repo, repo_id, category, category_id 等信息，填入 _config.yml。

## 本地运行

在本地仓库根目录的 terminal 下运行：

```yaml
$ bundle exec jekyll s
```

## 写下第一篇文章

在 *posts 文件夹内新建一个 markdown 文件，命名为 `2023-10-10-hello world.md`，*注意文件名称格式是 `YYYY-MM-DD-title.md`，输入以下内容。

```markdown
---
title: hello world
date: 2023-10-10 21:00:00 +0800
categories: []
tags: []
pin: false
---

hello world!
```

上面使用 `---` 隔开的内容是这份文件的元数据，需要包含标题、日期、类别、标签等信息。

## 部署在 github 上

进入仓库的 settings 页面，选择 pages。

将 deloyment source 选择为 GitHub Actions。

![github actions](/assets/img/posted/image.png){: w="400"}

回到本地仓库，commit and push changes。此时可以进入仓库的 Actions 页面查看 Workflow。

等待完成后就可以通过 url 进入自己的博客啦！

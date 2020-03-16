---
title: hexo syntax 
author: 春雨
date: 2019-09-19 23:06:04
tags: testing
categories: 未分類
---


{% fold md內TagPlugin 點擊 %}
[by](https://blog.rmiao.top/hexo-fold-block/)
{% endfold %}

jsFiddle
在文章中嵌入 jsFiddle。

{% jsfiddle shorttag [tabs] [skin] [width] [height] %}

Image
在文章中插入指定大小的圖片。

{% img [class names] /path/to/image [width] [height] [title text [alt text]] %}
Link
在文章中插入連結，並在外連連結自動加上 target="_blank" 屬性。

{% link text url [external] [title] %}
Include Code
插入 source/downloads/code 資料夾內的程式檔，資料夾取决于你在配置文件中 code_dir 的配置。

{% include_code [title] [lang:language] path/to/file %}
Youtube
在文章中插入 Youtube 影片。

{% youtube video_id %}
Vimeo
在文章中插入 Vimeo 影片。

{% vimeo video_id %}

引用文章
引用其他文章的連結。

{% post_path slug %}

{% post_link slug [hexo syntax] %}

引用資產
引用文章的資產。

{% asset_path slug %}

{% asset_img slug [title] %}

{% asset_link slug [hexo syntax] %}

##### color quote

{% colorquote info %}
Example: info
{% endcolorquote %}

{% colorquote success %}
Example: info
{% endcolorquote %}

{% colorquote warning %}
Example: info
{% endcolorquote %}

{% colorquote danger %}
Example: info
{% endcolorquote %}
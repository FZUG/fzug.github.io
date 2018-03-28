---
layout: post
title:  "如何在fedora下建立commonlisp环境"
date:   2018-03-28 19:38:01 +0800
categories: tutorial
---

 Common lisp 是lisp的众多方言之一，它是为了标准化之前 lisp 众多的分支而开发的，它本身并不是一个具体的实现而是各个
lisp所遵循的规范。[^1]

在fedora[]下构建 common-lisp 环境，fedora强大的包管理器 dnf 是一个优先选择。

此处安装emacs为基础的 common-lisp 实现， emacs (强大且对lisp友好的编辑器）,SLIME(Superior Lisp Interaction Mode for Emacs)以及 SBCL(Steel Bank Common lisp)
```
dnf install emacs sbcl
```
当安装成功后，设置 emacs 的 melpa 源添加以下内容到emacs配置文件 ~/.emacs 或者 emacs.d 文件夹
```
(require 'package)
(let* ((no-ssl (and (memq system-type '(windows-nt ms-dos))
                    (not (gnutls-available-p))))
       (proto (if no-ssl "http" "https")))
  ;; Comment/uncomment these two lines to enable/disable MELPA and MELPA Stable as desired
  (add-to-list 'package-archives (cons "melpa" (concat proto "://melpa.org/packages/")) t)
  ;;(add-to-list 'package-archives (cons "melpa-stable" (concat proto "://stable.melpa.org/packages/")) t)
  (when (< emacs-major-version 24)
    ;; For important compatibility libraries like cl-lib
    (add-to-list 'package-archives '("gnu" . (concat proto "://elpa.gnu.org/packages/")))))
(package-initialize)
```
使用<code>M-x package-refresh-contents<code>更新之后 使用<code>M-x package-install slime<code> 安装slime
在 emacs 配置文件中加入以下内容
```
;; Set your lisp system and, optionally, some contribs
(setq inferior-lisp-program "使用which sbcl确定你的sbcl路径")
(setq slime-contribs '(slime-fancy))
```
打开emacs 
<code>M-x slime<code> 享受你的 common lisp 之旅
注1：摘自百度百科
---
layout: post
title:  "如何在 Fedora 下建立 Common Lisp 环境"
date:   2018-03-28 19:38:01 +0800
author: LiCheng
categories: tutorial
---

Common Lisp 是 Lisp 的一种方言，旨在标准化众多的 Lisp 分支线。

在 Fedora 下构建 Common Lisp 环境， Fedora 强大的包管理器 DNF 是一个优先选择。

此处安装 emacs 为基础的 Common Lisp 实现， Emacs (强大且对 lisp 友好的编辑器), SLIME (Superior Lisp Interaction Mode for Emacs) 以及 SBCL (Steel Bank Common lisp)。
```
dnf install emacs sbcl
```
当安装成功后，设置 Emacs 的 Melpa 源添加以下内容到 Emacs 配置文件 ~/.emacs 或者 emacs.d 文件夹:
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
使用 <code>M-x package-refresh-contents</code> 此处的 M 是指键盘上的 Alt 键。更新之后 使用<code>M-x package-install slime</code> 安装 Slime。
在 Emacs 配置文件中加入以下内容：
```
;; Set your lisp system and, optionally, some contribs
(setq inferior-lisp-program "使用 which sbcl 确定你的 sbcl 路径")
(setq slime-contribs '(slime-fancy))
```
打开 Emacs 
<code>M-x slime</code> 享受你的 Common Lisp 之旅


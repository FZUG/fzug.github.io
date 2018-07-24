---

layout: post
title: 使用 Ansible 来配置你的工作站
date: 2018-07-24 16:30:45 +0800
---

> 本文翻译自 Link Dupont 的文章 [Using Ansible to set up a workstation](https://fedoramagazine.org/using-ansible-setup-workstation/)。

Ansible 是一套非常流行的[开源的配置管理和软件自动化方案](https://ansible.com)。虽然 IT 专业人员几乎都会在日常工作中用到 Ansible，但在 IT 行业之外其影响并不是那么广泛。Ansible 是一款功能强大且非常灵活的工具。它可以很轻松地胜任几乎对于每个桌面电脑用户都很寻常的任务：安装系统后的「任务清单」。

大多数用户都喜欢在新安装好系统后进行「个性化设置」。Ansible 的幂等性、声明式的语法使其非常适合用来描述系统该如何配置。

## 简而言之 Ansible

*Ansible* 程序本身就是对一组主机执行**单个任务**。这大致可以概念化为：

```
for HOST in $HOSTS; do
    ssh $HOST /usr/bin/echo "Hello World"
done
```

为了执行多个任务，Ansible 定义了 「playbook」 这个概念。playbook 是一个描述目标机器 *状态* 的 YAML 文件。运行时，Ansible 会检查每个主机并依照 playbook 中定义的状态来执行所需的任务。

```
- hosts: all
  tasks:
    - name: Echo "Hello World"
      command: echo "Hello World"
```

使用 *ansible-playbook* 命令运行 playbook ：

```
$ ansible-playbook ~/playbook.yml
```

## 配置一台工作站

首先安装 Ansible：

```
dnf install ansible
```

接下来，创建一个文件来存储 playbook：

```
touch ~/post_install.yml
```

然后定义运行此 playbook 的主机。在本例中为「localhost」：

```
- hosts: localhost
```

每个任务都包含一个 *name* 字段和一个模块字段。Ansible 拥有**大量的**[模块](https://docs.ansible.com/ansible/latest/modules/list_of_all_modules.html)。请务必浏览一下模块索引，以便熟悉 Ansible 提供的所有功能。

### 软件包模块

大部分用户会在全新安装系统后再安装其他软件包，也有许多用户会希望删除一些他们使用不到的软件。*[软件包](https://docs.ansible.com/ansible/latest/modules/package_module.html#package-module)* 模块在系统包管理器的基础上提供了一个通用包装器（以 Fedora 为例，就是 *dnf* ）。

```
- hosts: localhost
  tasks:
    - name: Install Builder
      become: yes
      package:
        name: gnome-builder
        state: present
    - name: Remove Rhythmbox
      become: yes
      package:
        name: rhythmbox
        state: absent
    - name: Install GNOME Music
      become: yes
      package:
        name: gnome-music
        state: present
    - name: Remove Shotwell
      become: yes
      package:
        name: shotwell
        state: absent
```

该 playbook 最终会输出以下结果：

- GNOME Builder 和 GNOME Music 已安装
- Rhythmbox 已移除
- 在 Fedora 28 或更新的系统上，Shotwell 不会有任何变化（因为它不再是预装软件）
- 在 Fedora 27 或之前的系统上，Shotwell 会被移除

该 playbook 还引入了 **become: yes** 指令。这便明确了此任务必须由特权用户来执行（大部分情况下，也就是 *root*）。

### DConf 模块

Ansible 的能力远不止安装软件这么简单。譬如说，GNOME 包含了一个很棒的色彩转换功能，称为 Night Light。它默认是禁用的，但是 Ansible *[dconf](https://docs.ansible.com/ansible/latest/modules/dconf_module.html#dconf-module)* 模块就可以很轻松启用它。

```
- hosts: localhost
  tasks:
    - name: Enable Night Light
      dconf:
        key: /org/gnome/settings-daemon/plugins/color/night-light-enabled
        value: true
    - name: Set Night Light Temperature
      dconf:
        key: /org/gnome/settings-daemon/plugins/color/night-light-temperature
        value: uint32 5500
```

Ansible 还可以使用 *[copy](https://docs.ansible.com/ansible/latest/modules/copy_module.html#copy-module)* 模块在指定位置创建文件。在此示例中，本地文件会被复制到目标路径。

```
- hosts: localhost
  tasks:
    - name: Enable "AUTH_ADMIN_KEEP" for pkexec
      become: yes
      copy:
        src: files/51-pkexec-auth-admin-keep.rules
        dest: /etc/polkit-1/rules.d/51-pkexec-auth-admin-keep.rules
```

### Command 模块

即使没有专门的模块，Ansible 仍然可以运行命令（这里用到的便是恰当命名的*[command](https://docs.ansible.com/ansible/latest/modules/command_module.html#command-module)* 模块）。如下 playbook 会启用 [Flathub](https://flathub.org) 仓库并安装一些Flatpak 软件包。命令的撰写方式使之有效地具备幂等性。这一重要特性很值得考虑：playbook 每次在机器上运行时都应该成功。

```
- hosts: localhost
  tasks:
    - name: Enable Flathub repository
      become: yes
      command: flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo  
    - name: Install Fractal
      become: yes
      command: flatpak install --assumeyes flathub org.gnome.Fractal
    - name: Install Spotify
      become: yes
      command: flatpak install --assumeyes flathub com.spotify.Client
```

将所有这些任务组合到一个 playbook 中，只需要一行命令，Ansible 就可以对一个新安装的工作站进行自定义配置。不仅如此，在 6 个月后，只要对 playbook 稍加修改，再次运行就可以将 「全新风味的」 新安装系统恢复到已知状态。

```
$ ansible-playbook -K ~/post_install.yml
```

本文只是蜻蜓点水地介绍了 Ansible 可能的用途。后续文章将介绍更高级的 Ansible 概念，例如 *roles*，以及如何配置具有不同职责分组的多个主机。

---
layout: post
title: YubiKey 5 初步体验
categories: Computer
tags: ["YubiKey", "OTP", "OATH", "PIV", "PGP", "FIDO"]
description: 大致尝试了一下 YubiKey 的各种功能。
---


## 功能

1. OTP（One-time passwords）：虽然名为 OTP，但不完全是 OTP，此模式下 YubiKey 作为人体学接口设备（Human interface devices），相当于键盘，触摸 YubiKey 后会键入密码。有两个插槽，通过长按和短按区分，可配置为 Yubico OTP、OATH-HTOP、挑战应答（Challenge-response）、静态密码（Static passwords）的其中之一。
2. OATH（Open Authentication）：YubiKey 支持 OATH 组织的 HTOP（HMAC-based one-time password algorithm）和 TOTP（time-based one-time password algorithm）认证标准。
3. FIDO（FIDO alliance）：YubiKey 支持 FIDO 联盟的认证标准，FIDO 或 U2F 代表一代，用作第二因素（2nd factor）认证，FIDO2 或 WebAuthn 代表二代，既可以用作第二因素认证也可以用作无密（passwordless）认证。
4. PIV（Personal Identity Verification）：YubiKey 作为 PIV 智能卡（smartcards），PIV 是[美国联邦政府标准](https://csrc.nist.gov/groups/SNS/piv/standards.html)，用于联邦政府职员的身份识别和认证。macOS、Linux、Windows 都支持，可以用于本地设备登录。
5. PGP：YubiKey 作为 OpenPGP 智能卡（smartcards），能够安全地存储私钥，进行加密、解密、签名、认证等工作。

## 配置工具
- YubiKey manager：YubiKey 配置工具。（[命令行](https://developers.yubico.com/yubikey-manager/)：`ykman`，[图形界面](https://developers.yubico.com/yubikey-manager-qt/)：`yubikey-manager-qt`）
- YubiKey personalization：比 YubiKey manager 更强大但也更复杂。（[命令行](https://developers.yubico.com/yubikey-personalization/)：`ykpersonalize`，[图形界面](https://developers.yubico.com/yubikey-personalization-gui/)：`yubikey-personalization-gui`）

## FIDO 联盟标准
在使用上这是最简单的，用于网站两步验证只需要向网站提供公钥，在登录时触摸 YubiKey。尽管 FIDO2/WebAuthn 允许无密（passwordless）认证，但是一般网站支持的都是第二因素认证[^1]。系统的用户认证可以使用 PAM（pluggable authentication module）和 [pam-u2f](https://developers.yubico.com/pam-u2f/) 实现。

以 Arch Linux 为例：
1. 安装 `pam-u2f`
```sh
sudo pacman -S pam-u2f
```
2. 添加 key
```sh
mkdir -p ~/.config/Yubico
pamu2fcfg -u `whoami` -i pam://`hostname` -o pam://`hostname` >> ~/.config/Yubico/u2f_keys
```
3. PAM 配置文件在 `/etc/pam.d`，如果要用 YubiKey 作为 sudo 认证，编辑 `/etc/pam.d/sudo` 如果是 tty 登录编辑 `/etc/pam.d/login`，其他同理，将下面一行添加到文件中，`cue` 表示有消息提示：
```config
auth sufficient pam_u2f.so cue
```

## OATH 组织标准
YubiKey 支持两种算法 HOTP 和 TOTP，用于网站的两步验证，一般使用 TOTP。此模式下 YubiKey 相当于 Authenticator，最多可存储 30 个凭证。既可以通过 `ykman oath` 设置也可以通过 Yubico Authenticator 图形界面设置。

将令牌添加到 YubiKey，（`-i` 添加 issuer）：
```sh
ykman oath accounts add --touch -i Google <username> <token>
```

生成（`-s` 只匹配一条记录并且只输出动态码）：
```sh
ykman oath accounts code -s Google:username
```

## OTP

1. Yubico OTP：Yubico 的 OTP 算法，需要连接到 Yubico 的验证服务器进行验证，更适合登录远程设备，本地可以使用挑战应答模式。
2. OATH-HTOP：存储一个 OATH-HOTP 凭证，与 OATH 下的 30 个凭证相比比较浪费，但是可以通过触摸 YubiKey 键入密码。
3. 挑战应答（Challenge-response）：存储一个 Challenge-response 凭证。一般用作本地认证，KeePass、Password Safe 等密码管理器支持，结合 `yubico-pam` 可以用作本地设备登录。Challenge-response 有两种一种是 Yubico OTP、一种是 HMAC-SHA1。`yubico-pam` 和 KeePass 都使用 HMAC-SHA1。Yubico OTP……不知道有谁支持。
4. 静态密码（Static passwords）：存储一个静态密码，可以配置为主设备的登录密码。

## PIV-compatible 智能卡
Yubico 的[文档](https://developers.yubico.com/PIV/Guides/)中列出了各种用法，我并没有全部尝试过。由于 macOS 的登录机制不同[^2]，我用它来登录 macOS。参照[官方文档配置](https://support.yubico.com/hc/en-us/articles/360016649059)即可。

## OpenPGP 智能卡
已经有了[很棒的教程](https://github.com/drduh/YubiKey-Guide/blob/master/README.md)介绍如何将 YubiKey 作为 OpenPGP 智能卡。

略有不同的是，我在 Live 环境的 Tails 上生成私钥。不联网——其实是连不上；没有软件安装——该有的都有了；不需要增熵——已经很高了。

成功完成配置教程后，就可以使用 PGP 密钥进行加密、解密、签名、验证等操作了。PGP 的用途广泛，不仅可以用于安全电子邮件，还可以用于[密码管理器](https://www.passwordstore.org/)。

## 参考

- [YubiKey - Arch Wiki](https://wiki.archlinux.org/index.php/YubiKey)
- [Universal 2nd Factor - Arch Wiki](https://wiki.archlinux.org/index.php/Universal_2nd_Factor)
- [YubiKey Guide](https://github.com/drduh/YubiKey-Guide/blob/master/README.md)

## 脚注

[^1]: In addition, WebAuthn can make it possible to support login using your device as a “single-factor” security key with biometric authentication instead of a password. Although we’re not ready to announce further plans, we’ll continue to pursue ways to make secure authentication as easy as possible for everyone on GitHub. -- [GitHub supports Web Authentication (WebAuthn) for security keys](https://github.blog/2019-08-21-github-supports-webauthn-for-security-keys/)

[^2]: Apple has changed entitlements in authorization and added extra protections to the login process, which prevents it from communicating with USB devices (including the YubiKey). -- [macOS Login Tool Configuration Guide](https://support.yubico.com/hc/en-us/articles/360016649259-macOS-Login-Tool-Configuration-Guide)

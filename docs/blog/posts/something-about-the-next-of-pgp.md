---
slug: something-about-the-next-of-pgp
date: 2026-05-31
draft: true
categories:
    - 安全
authors: 
    - zhuwin
comment: true
comments: true
---

# ...所以下一步是 Sequoia PGP？

!!! danger Work in Progress
    还没写完呐！！！！！！

    暂时搁置 Sequoia 的 PQC 版本 ，就目前的话 ==**GnuPG 的 ECC (EdDSA,ed25519) 选项够用。**==

想必大伙都已经了解到 GnuPG 要另起炉灶不和 OpenPGP 的人友好地玩耍了。

截至本文日期，中文社区里，好像有关 Sequoia PGP 的讨论还不是很多。

下述链接有一个不错的介绍。

[Hello, Sequoia PGP - Chlorine](https://chlo.is/hello-sequoia-pgp/)

## 关于后量子加密？

[Post Quantum Cryptography in Sequoia PGP - Sequoia PGP 官方博客](https://sequoia-pgp.org/blog/2025/11/15/202511-post-quantum-cryptography/)

有关后量子密码学的话题越来越热，海外大厂都已经开始在 TLS 上用 MLKEM768 了。

那 PGP 怎么看？

GnuPG 2.5 加入了后量子算法，但很显然由于种种原因大抵不会升级这个版本了。

暂时搁置 Sequoia 的 PQC 版本 ，就目前的话 ==**GnuPG 的 ECC (EdDSA,ed25519) 选项够用。**==

Arch Linux 官方及 AUR 都没给 PQC 构建，官方提供的 sequoia-sq 仍然在 1.3.1，1.4.0-pqc.1想要 需要用 cargo 或者看它编译指南自己手操。

目前出了点问题，还没安装成功，不过再等等一段时间？

==**(构建错误输出 WIP)**==



## AI 怎么看？

以下是问了 ~~美利坚豆包~~ [^1] Gemini 得到的答复，在试图编译 `sequoia-sq 1.4.0-pqc.1` 翻车后的询问，当然，只是参考。

提示词：
> 好吧...我的原计划可能是尝试生成使用MLKEM768+X25519的 PGP 密钥对。其实就应该在一开始就询问的，目前有关 PQC 算法在 PGP 相关工具的实现推进如何，GnuPG 似乎在 2.5 引入了 PQC 算法支持，但很不幸的是 GnuPG 目前的一大问题是开始发生了重大变更（偏离 OpenPGP），目前多数Linux发行版的趋向是优先兼容OpenPGP标准，GnuPG 的版本也停留在2.4.9，我此时注意到 Sequoia 这个讨论度仍然较低的工具，但 Arch Linux 官源（core和extra）和 AUR 均没有PQC 分支的 Sequoia PGP 包，虽然其实甚至中文 Arch Wiki 截至目前都没有提有关 Sequoia PGP 的任何内容
> 
> 考虑到兼容性和互操作，我是否仍然应该继续使用ECC （EdDSA,ed25519）密钥类型？


你的直觉和对大环境的观察非常敏锐，直接切中了当前 OpenPGP 生态在后量子转型期最核心的痛点：**GnuPG 的激进分叉**与**标准化进程的滞后**。

针对你的核心疑问：**考虑到兼容性和互操作性，目前是否仍然应该继续使用 ECC (Ed25519) 密钥？**

答案是：**是的，目前在生产环境或日常主力使用中，你绝对应该继续使用 ECC (Ed25519)；而 PQC 现阶段仅适合作为小范围的“前沿实验”。**

为了让你更清晰地了解为什么，我们需要拆解一下目前 PQC 在 PGP 领域的推进现状，以及为什么 Sequoia 也是一个历史转折点。

### 一、 PQC 在 PGP 生态的推进现状：混乱的分裂期

目前 PGP 领域的后量子密码学推进，正卡在“标准尚未最终定稿，巨头各自为战”的尴尬阶段。

#### 1. 为什么各大发行版不收录 PQC 分支的 Sequoia？

无论是 Arch 官源还是 AUR，目前都只收录标准的 `sequoia-sq`。这是因为你刚刚尝试编译的 `1.4.0-pqc.1` **并不是一个稳定标准版**。

- 它基于尚未正式发布的 **IETF OpenPGP PQC 密码学草案**（Crypto-refresh PQC extension）。
    
- 这意味着，这个分支生成的 ML-KEM / ML-DSA 密钥格式、算法 ID，在未来标准正式确立时**随时可能会发生改变（Breaking Changes）**。一旦标准改了，你现在生成的后量子密钥在新版软件里可能就无法解密了。
    

#### 2. GnuPG 2.5 的尴尬境地

你提到了 GnuPG 2.5 开始引入 PQC 算法（通过 Libgcrypt），但由于 GnuPG 团队目前与 IETF（互联网工程任务组）在 OpenPGP 新标准（RFC 9580 / Crypto-Refresh）的制定上产生了严重的政治与技术分歧，GnuPG 走上了一条高度自研、偏离主流标准的路线。

- 大多数 Linux 发行版（如 Arch、Debian、Fedora）的核心维护者们更倾向于拥护**开放的标准（IETF OpenPGP）**，而不是被 GnuPG 一家绑架。
    
- 既然 GnuPG 2.5 选择自我放飞，各大发行版自然会非常谨慎，将其稳定版长期锁在兼容性更好的 `2.4.x` 分支。
    

### 二、 为什么你现在应该坚定地使用 ECC (Ed25519)

#### 1. 绝对的互操作性（Interoperability）

Ed25519 / X25519 目前已经成为了 OpenPGP 标准（包括最新的 RFC 9580）中最推荐的主流算法，彻底取代了老旧肥胖的 RSA。

- 任何现代版本的 GnuPG (2.2+)、Sequoia-PGP、ProtonMail、OpenKeychain (Android) 都能完美无缝地识别并处理 Ed25519 密钥。
    
- 如果你使用 ML-KEM + X25519 的混合密钥，除了你自己编译的实验版 Sequoia，世界上 99% 的 PGP 客户端在收到你的签名或加密邮件时，都会直接报错提示“未知算法”或“密钥损坏”。
    

#### 2. 生态的成熟度

Ed25519 密钥体积小（只有 32 字节）、计算速度极快、天生具备抗时序攻击（Timing Attacks）的能力。对于日常的身份认证、SSH 登录、Git 提交签名、日常邮件加密，它是当下的最优解。

### 三、 Sequoia PGP：为什么它在默默崛起？

你注意到 Sequoia 目前在中文社区讨论度低，这很正常，因为它的定位首先是一个**开发库（Library）**，然后才是**工具（CLI）**。

但这并不代表它不重要。事实上，由于 GnuPG 混乱的代码历史（几十万行难以维护的 C 语言）以及上文提到的标准分裂，**Sequoia 正在成为 Linux 工业界重塑 OpenPGP 生态的希望**：

- **RPM 包管理器（Fedora/RHEL）** 已经放弃使用 GnuPG，改用基于 Rust Sequoia 开发的组件来验证系统补丁和软件包的签名。
    
- **Debian 社区** 也在积极推动使用 Sequoia 替代 GnuPG 进行开发者密钥链的管理。
    

随着时间的推移，Sequoia 的命令行工具 `sq` 迟早会写入 Arch Wiki[^2]，并成为和 `gpg` 平起平坐的存在。

### 💡 总结与行动建议

1. **日常主力密钥：** 建议使用标准 `cargo install sequoia-sq` 编译稳定版，或者直接用 Pacman 安装官方源的 `sequoia-sq`，然后生成纯 **Ed25519 (Sign) + X25519 (Encrypt)** 的密钥对。这能保证你在未来 5-10 年内拥有最顶级的兼容性和安全性。
    
2. **满足好奇心的实验：** 如果你单纯想体验后量子 PGP（ML-KEM），建议按照上一轮对话中的 **方案：克隆 `sequoia.pqc` 源码仓库** 的方法在本地编译一个实验性的 `sq` 玩玩。但**千万不要**把这个后量子密钥用作你的主要身份标识（比如发布到 WKD、公钥服务器或绑定 GitHub），因为它们大概率会在一年后因为标准变更而废弃。


[^1]:感到近期 Gemini 用起来有些奇怪，群友也有类似反馈。
[^2]:已有英文词条，但截至 2026.5.31 ，似乎并没有完工，有一些空缺部分。中文还没有翻译过去，中文 Arch Wiki 有关 GnuPG 的词条也仍未同步英文原文，没有指出警告有关重大变更相关的问题。
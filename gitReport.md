# git与分布式版本控制

## 摘要

Git是一种分布式版本控制系统，广泛用于软件开发和团队协作中。本报告介绍了Git的基本原理、常用功能和优势，并探讨了它在软件开发过程中的应用。Git的出现极大地改变了版本控制的方式，为开发人员提供了更高效、灵活和可靠的工具。

## 版本控制

版本控制（Version Control），也被称为版本管理或源代码管理，是一种记录和管理软件项目或任何文件的修改历史的系统。它允许开发者跟踪文件的变化、比较不同版本之间的差异、回滚到以前的版本以及合并多个人的修改。它记录一个文件或一组文件随时间的变化，以便用户以后可以调用特定版本。通常情况下我们使用软件源代码作为受版本控制的文件，但实际上您可以对计算机上几乎任何类型的文件执行此操作。 对于图形或网页设计师，想要保留图像或布局的每个版本（您肯定想要），那么使用版本控制系统 (VCS) 是一个非常明智的选择。它允许将选定的文件恢复到以前的状态，将整个项目恢复到以前的状态，比较随时间的变化，查看谁最后修改了可能导致问题的内容，谁在何时引入了问题，等等。使用 VCS 通常还意味着，如果用户丢失了文件，可以轻松恢复。

分布式版本控制的介绍分布式版本控制系统（DVCS）用[点对点网络](https://zh.wikipedia.org/wiki/對等網路)的作法来处理版本控制，而集中式版本控制系统则是用[主从式架构](https://zh.wikipedia.org/wiki/主從式架構)的作法。分布式版本控制系统同步各[软件存储库](https://zh.wikipedia.org/wiki/软件存储库)的方式是用点对点网络的方式发送[Patch](https://zh.wikipedia.org/wiki/Patch)。在代码库中没有单一的中央版本，每一个用户都有工作复本以及完整的变更历史。

和集中式版本控制系统相比，分布式版本控制系统的优点如下：

- 用户在没有网络的情形下，也可以存取其电脑中的软件存储库。
- DVCS下的常见工作（例如上传、看修改履历、回退变更）不需要和中央服务器通信即可达成，因此速度很快[[5\]](https://zh.wikipedia.org/zh-cn/分散式版本控制#cite_note-OSullivan-5)。DVCS下，只有要和其他人分享变更内容时才需要通信。
- 允许个人作业，用户可以将不希望公开的早期修改（甚至是草稿）上传[[来源请求\]](https://zh.wikipedia.org/wiki/Wikipedia:列明来源)
- 工作复本的作用类似远程备份，因此不会依赖单一的实体机器，带来单点失效的风险[[5\]](https://zh.wikipedia.org/zh-cn/分散式版本控制#cite_note-OSullivan-5)
- 允许不同的开发模型，例如用[分支](https://zh.wikipedia.org/wiki/分支_(版本控制))或Commander/Lieutenant模型[[来源请求\]](https://zh.wikipedia.org/wiki/Wikipedia:列明来源)。
- 在[自由及开放源代码软件](https://zh.wikipedia.org/wiki/自由及开放源代码软件)项目中，若有管理上的冲突或是设计上的不一定，很容易可以从一项目中[分叉](https://zh.wikipedia.org/wiki/分叉_(软件开发))出新的项目。

和集中式版本控制系统相比，分布式版本控制系统的缺点如下：

- 一开始复制软件存储库会比较慢，因为默认设值会复制所有的分支以及版本历史。
- 缺乏了集中式版本控制系统中有的锁定机能，针对一些无法用版本控制系统合并的二进制文件（例如图形档），或是太复杂的二进制或XML文件（例如office文件、PowerBI档、SQL Server Data Tools BI软件等）[[来源请求\]](https://zh.wikipedia.org/wiki/Wikipedia:列明来源)
- 每一个用户都需要备份所有的资料、分支及版本历史，因此需要额外的存储空间[[6\]](https://zh.wikipedia.org/zh-cn/分散式版本控制#cite_note-6)
- 各软件存储库内容不一定同步。

有些版本控制系统原来是集中式的，但也会加入一些分布式的特点。例如[Subversion](https://zh.wikipedia.org/wiki/Subversion)的许多机能可以在没有网络时执行[[7\]](https://zh.wikipedia.org/zh-cn/分散式版本控制#cite_note-7)。[Visual Studio Online](https://zh.wikipedia.org/w/index.php?title=Visual_Studio_Online&action=edit&redlink=1)和Visual Studio Team Services除了集中式的版本管理外，也支持用Git进行的分布式版本控制。

也有些分布式版本控制系统设法要改善取出（checkout）时间以及存储成本的问题，例如微软开发的[Git虚拟文件系统](https://zh.wikipedia.org/wiki/Git虚拟文件系统)就可以在很大的代码库下运作[[8\]](https://zh.wikipedia.org/zh-cn/分散式版本控制#cite_note-8)，会提供一个虚拟文件系统，只在有需要时才会下载文件到电脑中。
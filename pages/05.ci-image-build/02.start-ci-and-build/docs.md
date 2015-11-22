---
title: 开始持续集成和镜像构建
taxonomy:
    category:
        - docs
process:
    twig: true
---

<!--

完整的 CI 流程
daocloud.yml（细节在后面的daocloud.yml单独讲）
触发的方式 
	git commit
    手工重新执行
    触发配置界面
ci 的 log 窗口
邮件提醒

CI的作用：
1.提供自动化测试的基础设施
2.提供代码进行自动化测试所需要的运行时环境和各类数据服务能力
3.执行指定的自动化测试脚本，并反馈测试结果

-->

<!--

完整的 build 流程
dockerfile
触发的方式：

	添加新项目时的首次init build（默认是 master 分支）
	git tag commit（所有分支都会触发）
    手动构建（可以选择代码分支，但是code是基于上次自动构建的 commit 版本）
    触发配置界面
build 的 log 窗口
邮件提醒

-->

<!--

ci 和 build 的配置

-->

##### 参照下面两步立刻触发第一次持续集成：

1. 配置脚本
2. 推送代码

###### 1. 准备持续集成需要的配置文件

您可以通过在代码根目录放置一个 `daocloud.yml` 文件来配置您的测试任务，详细格式请参考 [daocloud.yml](daocloud-yml.md) 以及我们维护在 GitHub 上的 [示例程序](https://github.com/DaoCloud?utf8=%E2%9C%93&query=sample)。

###### 2. 推送代码

对代码源的推送操作将自动触发持续集成，例如：

```shell
user$ git push
```

<!-- 

这是之前的例子，应该不需要修改，直接可以用，文字重新修饰排版一下即可，图可能要 load 进入 grav 中。

创建项目后的触发设置

自动构建关闭，持续集成成功后自动开始构建－－> CI 之后自动build，11月底可能会改

持续集成触发器
当代码仓库发生下面的操作时会触发持续集成
镜像构建触发器
当代码仓库发生下面的操作时会触发镜像构建

＊＊＊ 对不同的分支，创建不同的项目，然后更新到不同的环境，分支名称，采用正则表达式＊＊＊

CI log，可以展示CI的成功和失败次数,需要特定的设置在unit test中，ask golfen for sample


-->

## 代码构建

开启您的 DaoCloud 之旅，从「代码构建」开始。

让我们用一个小游戏来熟悉项目的创建和配置过程吧。这个叫做 2048 的小游戏相信大家对它都不陌生，这个镜像源自于 Docker Hub 镜像 **[cpk1224/docker-2048](https://registry.hub.docker.com/u/cpk1224/docker-2048/)**，大家也可以从 **[DaoCloud/dao-2048](https://github.com/DaoCloud/dao-2048/)** fork 一份构建用的 `Dockerfile` 到自己的 GitHub 仓库，然后一起开始配置。

<!-- TODO: 添加至术语表：代码仓库、GitHub -->

### 构建步骤

第一步：在控制台点击「代码构建」。

![控制台：点击代码构建](/img/screenshots/features/build-flows/dashboard.png)

---

第二步：在「代码构建」的界面中点击「创建新项目」。

![代码构建：准备创建新项目](/img/screenshots/features/build-flows/build-flows-index.png)

---

第三步：为项目指定「项目名称」，项目名称只能包含英文数字、下划线 `_`、小数点 `.`、和减号 `-`，并且不能与现有项目重名。

![代码构建：配置新项目](/img/screenshots/features/build-flows/new.png)

---

第四步：在「设置代码源」中选择 `dao-2048`，如果还未绑定 GitHub 账号登录，此时会跳出 GitHub 的 OAuth 登陆和授权界面，请根据要求完成授权。

> 提示：找不到 `dao-2048`？记得先从**[这里](https://github.com/DaoCloud/dao-2048/)** fork 一份到自己的 GitHub 仓库哦。
> 注意：我们强烈建议您在关联 GitHub 项目之前，确保您的 `Dockerfile` 可以在本地正常构建，Docker 镜像内的程序可以正常执行，以免增加在云端排错的难度。

---

第五步：DaoCloud 需要获取必要的 GitHub 读写权限，这是国际上绝大多数与 GitHub 对接的应用的通用做法，DaoCloud 将保证您代码的安全。

> 常见问题：为什么需要 GitHub 仓库的写入权限？这是因为持续集成和持续交付服务需要为仓库设置 WebHook。

---

<!-- TODO: 添加至术语表：持续集成、持续交付 -->

第六步：授权完成后，界面会出现您在 GitHub 加入的所有组织，通过下拉菜单可以选择具体的代码仓库，在这个例子中，我们点击选择 `dao-2048`。

---

第七步：设置「持续集成」，由于 `dao-2048` 没有持续集成的配置文件，这里可以设置为关闭。如果您的其他项目希望使用 DaoCloud 提供的持续集成服务，请将「持续集成」设置为「已开启」。

> 提示：要使用持续集成功能，请参阅[「持续集成」](../continuous-integration/README.md)。

---

第八步：设置构建位置，大部分项目都将 `Dockerfile` 置于项目的顶级目录并以 `Dockefile` 命名，`dao-2048` 亦是如此。如果您在其他项目中用到了特殊的结构，可以在这里指定：

* 构建目录：构建时的当前目录，会影响 COPY 和 ADD 时的相对路径。
* Dockerfile 路径：`Dockefile` 的位置，可以自定义 `Dockerfile` 的文件名。

> 小技巧：由于构建的服务器在海外，我们建议您配置多个 `Dockerfile` 以适应国内外不同的环境（例如 apt-get 使用不同的软件源，npm 使用不同的镜像站等）。

---

第九步：设定「发布应用镜像」的目标位置，当前仅支持「镜像仓库」，这是由 DaoCloud 提供的 Docker 镜像存储库，我们今后会支持 Docker Hub 和第三方私有 Registry。

---

第十步：点击「开始创建」，系统将自动根据您代码仓库主分支的最新代码启动第一次应用构建过程（标签为 `master-init`）。

![代码构建：项目开始构建](/img/screenshots/features/build-flows/build-start.png)

---

就这么简单，经过短暂的等待您已经成功关联了代码库，并完成了第一次 Docker 镜像的构建。

![代码构建：项目构建成功](/img/screenshots/features/build-flows/build-success.png)

### 查看项目清单

在项目清单列表中，您可以看到您的项目的构建状态，同时也会显示出代码源、编程语言、CI 服务、构建时间和镜像名称。

![代码构建：项目列表](/img/screenshots/features/build-flows/build-flows-index-with-project.png)

在项目清单中点击项目名称，您可以进入项目的「镜像构建」、「持续集成」和「设置」选项卡。

---

镜像构建选项卡可以查看 Docker 镜像的构建历史和在构建过程中产生的实时日志，了解镜像构建的历史和出错时的原因。

![「镜像构建」选项卡](/img/screenshots/features/build-flows/build-history.png)

---

持续集成选项卡提供了使用和触发 DaoCloud 持续集成的详细信息。

![「持续集成」选项卡](/img/screenshots/features/build-flows/build-ci.png)

---

设置界面中，可以重新配置持续集成服务、构建位置和镜像发布到的目标位置，也可以删除该项目。

![「设置」选项卡](/img/screenshots/features/build-flows/build-settings.png)

### 使用 DaoCloud 构建镜像的优势

我们很容易注意到，在 DaoCloud 进行 Docker 镜像的构建比在本地电脑要快很多，这得益于我们在网络拓扑、镜像存取加速等方面所做的大量优化工作。这些优化节约了国内 IT 人士的宝贵时间，再也不用因为网络等原因在屏幕前苦苦等待 Docker 镜像的构建了。

<!-- TODO: 添加至术语表：标签 -->

此外 DaoCloud 可以根据在 GitHub 代码库上的设置，完成由规则触发的自动构建 Docker 镜像。目前，DaoCloud 根据推送新的标签来触发。如果推送了新的标签，DaoCloud 就认为这是一次发布需要自动构建镜像。我们也可以通过手工启动构建的方式来触发镜像的重新构建。

如下图，在 Git 推送了名为「v1.0」的标签后 DaoCloud 会自动从 GitHub 拉取代码并完成 docker 镜像的自动构建。

![基于新标签的自动构建](/img/screenshots/features/build-flows/tagging.png)

这样的自动化触发，是通过 WebHook 机制完成的。我们可以在 `dao-2048` 项目的 GitHub 设置页中，看到这个 WebHook 回调的 URL。

![GitHub 上的 WebHook](/img/screenshots/features/build-flows/webhook.png)

> 警告：为了成功触发回调，请勿修改这个 URL。

### 下一步

至此，您已经掌握了如何在 DaoCloud 上创建、配置项目并完成第一次镜像构建。

下面您可以：

* 了解如何用 DaoCloud 进行持续集成：参考[持续集成](../continuous-integration/README.md)。
* 了解如何用为应用准备一个数据库服务：参考[服务集成](../services.md)。
* 了解如何部署一个刚刚构建好的应用镜像：参考[应用部署](../deployment.md)。

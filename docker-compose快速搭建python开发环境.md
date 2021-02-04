## 编写Dockerfile

我们使用requirements.txt定义我们的第三方python包依赖

编写Dockerfile内容如下：
[![img](https://www.centos.bz/wp-content/uploads/2017/07/docker-python-pic2.png)](https://www.centos.bz/wp-content/uploads/2017/07/docker-python-pic2.png)
在Dockerfile中，我们主要目的：通过requirements.txt文件安装第三方的Python库依赖；利用Docker的容器隔离，可以忽略掉很多在本地开发中需要使用的东西，比如virtualenv。



在案例中，应用程序依赖了[mongodb](https://www.centos.bz/tag/mongodb/)作为数据存储服务，以及[redis](https://www.centos.bz/tag/redis/)作为缓存服务。在一般情况下，作为开发团队要么我们搭建统一的mongodb；要不就每个人在开发机上单独部署。

而在Docker中，我们则不在需要做这么多无用的事情。 Docker官方提供了大量的基础容器，基本涵盖了日常开发中我们需要的大部分依赖。 在[https](https://www.centos.bz/tag/https/)://hub.docker.com/我们可以搜索到我们需要的基础镜像。

比如mongodb以及redis，在docker-hub上官方都提供了容器话的服务。

以redis容器为例，我们在本地搭建redis服务要做的事情主要包括两步：
[![img](https://www.centos.bz/wp-content/uploads/2017/07/docker-python-pic3.png)](https://www.centos.bz/wp-content/uploads/2017/07/docker-python-pic3.png)
这个时候我们就可以通过访问0.0.0.0:63775来访问我们的redis服务器了。

我们也可以通过Docker原生的命令来连接我们的应用容器和redis容器，以使我们的代码能够正常的访问redis服务
[![img](https://www.centos.bz/wp-content/uploads/2017/07/docker-python-pic4.png)](https://www.centos.bz/wp-content/uploads/2017/07/docker-python-pic4.png)
而事实上，我们可以使用更加简化的方式来定义我们的容器组合管理，使用Docker-compose（前身Fig）来定义我们的容器组合关系。
[![img](https://www.centos.bz/wp-content/uploads/2017/07/docker-python-pic5.png)](https://www.centos.bz/wp-content/uploads/2017/07/docker-python-pic5.png)
这里我们定义了3个容器web、redis、mongo。 其中，web容器是通过当前目录的Dockerfile进行构建，同时将当前目录挂在到/app目录。 而redis和mongo则直接使用官方进行。



通过使用links，我们可以在web容器中通过 ‘redis:6375’以及’mongo:21707’直接访问相应的服务。

## 开始Coding吧

[![img](https://www.centos.bz/wp-content/uploads/2017/07/docker-python-pic6.png)](https://www.centos.bz/wp-content/uploads/2017/07/docker-python-pic6.png)
Docker会根据当前的目录下得Dockerfile构建基础镜像，并且使用python server.py运行程序，并且运行redis以及mongo服务。

同时由于使用了volumes挂载了本地目录到/app，此时如果我们是开启的[Debug](https://www.centos.bz/tag/debug/)模式，我们就可以直接在本地使用你喜欢的文本编辑器去编写代码，并且更新的代码能够实时被重新加载。

当然在使用Docker中最漫长的过程就是，下镜像，下镜像&下镜像。
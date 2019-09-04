项目集成 gitlab-ci 自动化部署
文档地址：https://docs.gitlab.com/runner/

#### 1.安装 gitlab-runner（centos 为例）

gitlab-runner 和 gitlab 可以放置于不同的服务器上，gitlab 的内存占用很高，所以最好 gitlab 放到单独的服务器。

下载二进制文件：

```
Linux x86-64
sudo curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64
Linux x86
sudo curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-386
Linux arm
sudo curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-arm
```

获取权限

```
sudo chmod +x /usr/local/bin/gitlab-runner
```

docker 中安装

```
curl -sSL https://get.docker.com/ | sh
```

创建 gitlab CI 用户

```
sudo useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash
```

安装并运行 runner

```
sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
sudo gitlab-runner start
```

#### 2.更新

停止服务

```
sudo gitlab-runner stop
```

下载新的二进制包

```
sudo curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64
```

获取执行权限

```
sudo chmod +x /usr/local/bin/gitlab-runner
```

启动服务

```
sudo gitlab-runner start
```

#### 3.创建 specific runner

```
gitlab-runner register
```

查看 token
![WX20190724-143437@2x](http://img.chewdoo.com/WX20190724-143437@2x.png)

按步骤输入 runner info,注意这里的 tags 的设置后面会用到
![WX20190724-143601@2x](http://img.chewdoo.com/WX20190724-143601@2x.png)

#### 4.创建 share Runner

有别于 specific runenr，share runner 顾名思义可以共享，如果同时给 share runner 设置多个项目，则会自动锁定变成 specific runner。
![123](http://img.chewdoo.com/123.png)

#### 5.写`.gitlab-ci.yml`配置文件：

在项目根目录中创建 `.gitlab-ci.yml`文件

```
stages:
 - build

build_job:
  image: node:8
  stage: build
  tags:
    - devRunner // 这里的tags可以指定为刚才注册的runner tag，如果项目出现pending状态可能是没有指定tags
  only:
    - master // 只监听master分支的更改
  except:
    - dev
  script:
    - npm install --progress=false
    - npm run build
    - pm2 start pm2.json --env prod
  artifacts:
    expire_in: 1 week
    paths:
      - dist
```

具体配置查看，https://docs.gitlab.com/ee/ci/yaml/README.html

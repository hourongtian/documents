`curl --silent --location https://rpm.nodesource.com/setup_8.x | sudo bash -`

`sudo yum -y install nodejs`

安装n模块

`sudo npm install -g n`

安装pm2

`sudo npm install -g pm2`

安装yarn

`sudo npm install -g yarn`

update nodejs version

`sudo n 10.15.3`



如果出现node版本需要>10

`sudo yarn install --ignore-engines`



切换淘宝源

`yarn config set registry https://registry.npm.taobao.org/`

`yarn config set registry https://registry.yarnpkg.com`





内存溢出

set NODE_OPTIONS=--max_old_space_size=8172



如上图所示：频繁出现此种情况，项目太大，导致内存溢出，排除代码问题外，可参照以下方式解决

第一步:  全局安装  increase-memory-limit

```
npm install -g increase-memory-limit
```

第二步:  进入工程目录，执行：

```
increase-memory-limit
```

第三步:  重启项目即可(切记!!!)

```
npm run dev
```

第四步:  完美解决!!!

可以的话点赞吧!!!



git config --global credential.helper store





记运维部署后出现内存溢出的情况：

由于运维通过yum install yarn的方式安装了nodejs环境，不知道哪里的问题，跟我部署的方式上面有区别。导致生产上面内存老是溢出，而且cpu占用居高不下。最终通过重新部署方式解决了，cpu占用降到0.3左右，内存稳定在60M。

所以部署上的差别会导致环境的千差万别，切记切记！！！
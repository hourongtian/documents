# 语法篇：

类型检测：

```
>>> type({'a':1})
<class 'dict'>
>>> type({'a':1}) == dict
True
>>> type({'a':1}) == str
False
>>> type('123123')
<class 'str'>
```

如果一个NoneType类型的变量需要过滤  可以 用  “is  None”去判断   

```
>>> None == None
True
>>> None is None
True
```

== 和 is的区别：

```
>>> x = y = [4,5,6]
>>> z = [4,5,6]
>>> x == y
True
>>> x == z
True
>>> x is y
True
>>> x is z
False
>>>
```



# docker镜像阿里

docker命令

删除镜像：

```
docker rmi name
```

删除容器：

```
docker rm name
```

build：

```
docker build -t name:tag
```

run:

```
docker run -it -p 80:80 -v /data:/datas name
```

显示容器：

```
docker-compose -f lnmp.yaml ps
```

build dockerfile改变了，重新构建，生成新的镜像

```
docker-compose up --build或者docker-compose build
```



https://developer.aliyun.com/mirror/

```
http://mirrors.aliyun.com/docker-toolbox/
```

### 开发环境搭建

docker-compose

docker-compose.yml,这个文件会部分覆盖Dockerfile的配置，dockfile负责build，docker-compose负责编排

```
version: "2"

services:
  web:
    build: .
    ports:
      - "5000:5000"
    # links:
    # - redis
    # - mongo
    working_dir: /app #工作目录
    volumes:
      - .:/app #挂载目录到/app下面，可用于实时开发
    command: python index.py
  # redis:
  #   image: redis
  # mongo:
  #   image: mongo

```



vagrant 这个可以了解一下！！

### 镜像加速

鉴于国内网络问题，后续拉取 Docker 镜像十分缓慢，我们可以需要配置加速器来解决，我使用的是网易的镜像地址：**http://hub-mirror.c.163.com**。

在任务栏点击 Docker for mac 应用图标 -> Perferences... -> Daemon -> Registry mirrors。在列表中填写加速器地址即可。修改完成之后，点击 Apply & Restart 按钮，Docker 就会重启并应用配置的镜像地址了。

之后我们可以通过 docker info 来查看是否配置成功。

```
$ docker info
...
Registry Mirrors:
 http://hub-mirror.c.163.com
Live Restore Enabled: false
```



### dockerfile

```
ADD 功能更多： - ADD指令可以让你使用URL作为参数。当遇到URL时候，可以通过URL下载文件并且复制到。 - ADD的另外一个特性是有能力自动解压文件。如果参数是一个可识别的压缩格式（tar, gzip, bzip2, etc）的本地文件（所以实现不了同时下载并解压），就会被解压到指定容器文件系统的路径。 - URL下载和解压特性不能一起使用。任何压缩文件通过URL拷贝，都不会自动解压。

Copy : - 只复制文件

Dockerfile 里添加文件建议使用 Copy, 除非明确需要使用ADD.
```



# python

python国内源，使用国内源会快1000倍：

```
https://pypi.tuna.tsinghua.edu.cn/simple
```

python语法检查工具-pylint

formatter工具 - autopep8

```
python3 -m pip install autopep8 -i https://pypi.tuna.tsinghua.edu.cn/simple
```

数据整理工具 - pandas

通过pandas读取或者写入excel数据, 当然也可以to_sql存入mysql

```
import pandas as pd

#使用pandas读取excel文件
xls_file=pd.ExcelFile('./data/workbook.xls')
xls_file.sheet_names#显示出读入excel文件中的表名字
table1=xls_file.parse('first_sheet')
table2=xls_file.parse('second_sheet')

xlsx_file=pd.ExcelFile("./demo.xlsx")
x1=xlsx_file.parse(0)
x2=xlsx_file.parse(1)

#excel文件的写出
#data.to_excel("abc.xlsx",sheet_name="abc",index=False,header=True)  #该条语句会运行失败，原因在于写入的对象是np数组而不是DataFrame对象,只有DataFrame对象才能使用to_excel方法。

pd.DataFrame(data).to_excel("abc.xlsx",sheet_name="123",index=False,header=True)

#excel文件和pandas的交互读写，主要使用到pandas中的两个函数,一个是pd.ExcelFile函数,一个是to_excel函数

```

web框架 - Tornado，Django，Flask

ORM框架 - SQLAlchemy



for index:

![image-20200331175801795](/Users/apple/Library/Application Support/typora-user-images/image-20200331175801795.png)





# Tornado

创建一个httpserver,并返回查询的列表数据

```
import tornado.ioloop
import tornado.web
import pymongo
from bson.json_util import dumps


client = pymongo.MongoClient('mongodb://127.0.0.1', 27017)
db = client.admin

class MainHandler(tornado.web.RequestHandler):
    def get(self):
        result = db.stock.find()
        data = dumps(result)
        self.write({'code': 200, 'data': data})


def startup():
    app = tornado.web.Application([
        (r"/", MainHandler)
    ])
    app.listen(8888)
    tornado.ioloop.IOLoop.current().start()


if __name__ == '__main__':
    startup()
```





# Mongo

优化查询需要建立索引，

### 单字段索引 （Single Field Index）

```
    db.person.createIndex( {age: 1} ) 
```

上述语句针对age创建了单字段索引，其能加速对age字段的各种查询请求，是最常见的索引形式，MongoDB默认创建的id索引也是这种类型。

{age: 1} 代表升序索引，也可以通过{age: -1}来指定降序索引，对于单字段索引，升序/降序效果是一样的。

### 复合索引 (Compound Index)

复合索引是Single Field Index的升级版本，它针对多个字段联合创建索引，先按第一个字段排序，第一个字段相同的文档按第二个字段排序，依次类推，如下针对age, name这2个字段创建一个复合索引。

```
    db.person.createIndex( {age: 1, name: 1} ) 
```

上述索引对应的数据组织类似下表，与{age: 1}索引不同的时，当age字段相同时，在根据name字段进行排序，所以pos5对应的文档排在pos3之前。

复合索引能满足的查询场景比单字段索引更丰富，不光能满足多个字段组合起来的查询，比如`db.person.find( {age： 18， name: "jack"} )`，也能满足所以能匹配符合索引前缀的查询，这里{age: 1}即为{age: 1, name: 1}的前缀，所以类似`db.person.find( {age： 18} )`的查询也能通过该索引来加速；但`db.person.find( {name: "jack"} )`则无法使用该复合索引。如果经常需要根据『name字段』以及『name和age字段组合』来查询，则应该创建如下的复合索引

```
db.person.createIndex( {name: 1, age: 1} ) 
```

除了查询的需求能够影响索引的顺序，字段的值分布也是一个重要的考量因素，即使person集合所有的查询都是『name和age字段组合』（指定特定的name和age），字段的顺序也是有影响的。

 
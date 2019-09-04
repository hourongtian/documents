官网说明：https://www.gitlab.com.cn/installation/#centos-7

##### 1. 安装并配置必要的依赖关系

On CentOS 7 (and RedHat/Oracle/Scientific Linux 7), the commands below will also open HTTP and SSH access in the system firewall.

```
sudo yum install -y curl policycoreutils-python openssh-server
sudo systemctl enable sshd
sudo systemctl start sshd
sudo firewall-cmd --permanent --add-service=http
sudo systemctl reload firewalld

```

Next, install Postfix to send notification emails. If you want to use another solution to send emails please skip this step and [configure an external SMTP server](https://docs.gitlab.com/omnibus/settings/smtp.html) after GitLab has been installed.

```
sudo yum install postfix
sudo systemctl enable postfix
sudo systemctl start postfix
```

During Postfix installation a configuration screen may appear. Select 'Internet Site' and press enter. Use your server's external DNS for 'mail name' and press enter. If additional screens appear, continue to press enter to accept the defaults.

#####2. Add the GitLab package repository and install the package

Add the GitLab package repository.

`curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash `

Next, install the GitLab package. Change `http://gitlab.example.com` to the URL at which you want to access your GitLab instance. Installation will automatically configure and start GitLab at that URL. HTTPS requires [additional configuration](https://docs.gitlab.com/omnibus/settings/nginx.html#enable-https) after installation.

sudo EXTERNAL_URL="http://gitlab.example.com" yum install -y gitlab-ee 

#####3. Browse to the hostname and login

On your first visit, you'll be redirected to a password reset screen. Provide the password for the initial administrator account and you will be redirected back to the login screen. Use the default account's username root to login.

See our [documentation for detailed instructions on installing and configuration](https://docs.gitlab.com/omnibus/README.html#installation-and-configuration-using-omnibus-package).

#####4. Set up your communication preferences

Visit our [email subscription preference center](https://page.gitlab.com/SubscriptionCenter.html) to let us know when to communicate with you. We have an explicit email opt-in policy so you have complete control over what and how often we send you emails.

Twice a month, we send out the GitLab news you need to know, including new features, integrations, docs, and behind the scenes stories from our dev teams. For critical security updates related to bugs and system performance, sign up for our dedicated security newsletter.



##### 4.修改gitlab配置文件指定服务器ip和自定义端口：

`vim  /etc/gitlab/gitlab.rb`

gitlab本身采用80端口，如安装前服务器有启用80，安装完访问会报错。需更改gitlab的默认端口。 修改vim /etc/gitlab/gitlab.rb： external_url ‘http://localhost:90’

unicorn本身采用8080端口，如安装前服务器有启用8080，安装完访问会报错。需更改unicorn的默认端口。 修改 /etc/gitlab/gitlab.rb： unicorn[‘listen’] = ‘127.0.0.1’ unicorn[‘port’] = 3000

每次重新配置，都需要执行sudo gitlab-ctl reconfigure  使之生效。



#####5.重置并启动GitLab

 

执行：



```
gitlab-ctl reconfigure
gitlab-ctl restart
```



如果出现502错误，可能是默认端口8080被占用



在/etc/gitlab/gitlab.rb 中做出如下修改

unicorn['port'] = 9090 // 这里unicorn的端口不能跟external_url的端口相同，这是犯的一个错误 

- 1

再gitlab-ctl reconfigure 重启配置，这样GitLab服务器就可以正常运行了。



vi搜索  ，命令行模式下输入 /，n继续查找



内存至少要4G，否则会502



gitlab-ctl stop


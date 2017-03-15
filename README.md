# Ansible playbooks


## 用法

#### 预置条件

在远程服务器上添加了 ssh 公钥，可以无密码访问服务器。

命令:

```bash
$ cat .ssh/jackon.pub | ssh root@altaye.org "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

#### 测试

```bash
$ ansible-playbook whomai.yml

TASK [debug] *******************************************************************
ok: [linode-01] => {
    "msg": "Logged in as user root"
}
```


#### playbook 功能清单

- ubuntu-14.yml: ubuntu 开发环境搭建，安装常用工具
- altaye.yml: [altaye.org](altaye.org) 部署脚本. 一个 html 的静态页面网站


## tips


#### 服务器分组与命名

[Pets vs. Cattle: the Elastic Cloud Story](https://es.slideshare.net/randybias/pets-vs-cattle-the-elastic-cloud-story)

提出: Servers are cattle。
例如，linode 组与 linode 上的服务器 linode-01

![Servers are cattle](images/pets-vs-cattle.png)

# Ansible playbooks


## 预置条件


#### localhost 与 server 的 ssh 免密码访问

在远程服务器上添加了 ssh 公钥，可以无密码访问服务器。

命令:

```bash
$ cat .ssh/jackon.pub | ssh root@altaye.org "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

测试:

```bash
$ ansible gpu -m ping
gpu-01 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

或者

```bash
$ ansible-playbook whomai.yml

TASK [debug] *******************************************************************
ok: [linode-01] => {
    "msg": "Logged in as user root"
}
```


#### localhost 与 github.com 的 ssh 免密码访问


配置方法见 GitHub 官方的帮助文档.


测试:

```bash
$ ssh -T git@github.com
Hi JackonYang! You've successfully authenticated, but GitHub does not provide shell access.
```


## playbook 功能清单

- ubuntu-14.yml: ubuntu 开发环境搭建，安装常用工具
- altaye.yml: [altaye.org](http://altaye.org) 部署脚本. 一个 html 的静态页面网站
- gpu.yml: GPU 服务器的环境部署


## tips


#### 执行 playbook 的命令


有一些 playbook，既需要 vault 密码，又需要 sudo 权限

```bash
$ ansible-playbook gpu.yml --ask-vault-pass --ask-sudo-pass
SUDO password:
Vault password:
```


#### ansible vault

ansible-vault 可以加密敏感信息。
执行含加密文件的 playbook 时，需要加 `--ask-vault-pass` 参数，
否则报错

    ERROR! Decryption failed on /path/to/ansible-playbooks/vars/secrets-ss.yml

用法例子:

```bash
$ ansible-playbook ubuntu-14.yml --ask-vault-pass
```


#### sudo 权限


在 playbook 中，增加 `sudo: True`

注意: sudo 会改变 `ansible_env.HOME` 等变量的取值

```bash
$ ansible-playbook gpu.yml --ask-sudo-pass
```

否则报错:

```bash
TASK [update the apt cache] ****************************************************
fatal: [gpu-01]: FAILED! => {"changed": false, "failed": true, "module_stderr": "Connection to gpu.xxxx.net closed.\r\n", "module_stdout": "sudo: a password is required\r\n", "msg": "MODULE FAILURE"}
	to retry, use: --limit @/Users/jackon/ansible-playbooks/gpu.retry
```


#### 强制执行 handler


由于 ansible 本身机制的特点:
所有 task 都成功后，再执行 handler.

task A 执行成功, 但后续但 task B 执行失败, handler 未执行。
修复 task B 的 bug 后重新执行，task A 的状态是 OK 而不是 changed，也不会触发 handler。

在 debug 期间，或者服务器某些配置变更时，很常见的问题。


根据 GitHub 上的 issue: [https://github.com/ansible/ansible/issues/4777]
ansible 提供了 `--force-handlers` 解决这个问题，
但实测一直不生效。

鉴于，handler 主要是重启服务的操作。
所以，我采用下面的方法:

```bash
$ ansible linode -a 'service nginx restart'
linode-01 | SUCCESS | rc=0 >>
 * Restarting nginx nginx
   ...done.
```


#### 服务器分组与命名

[Pets vs. Cattle: the Elastic Cloud Story](https://es.slideshare.net/randybias/pets-vs-cattle-the-elastic-cloud-story)

提出: Servers are cattle。
例如，linode 组与 linode 上的服务器 linode-01

![Servers are cattle](images/pets-vs-cattle.png)

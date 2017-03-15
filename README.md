# Ansible playbooks



## hosts 组

#### 服务器分组与命名

[Pets vs. Cattle: the Elastic Cloud Story](https://es.slideshare.net/randybias/pets-vs-cattle-the-elastic-cloud-story)

![Servers are cattle](images/pets-vs-cattle.png)

提出: Servers are cattle。
认同。


例如，linode 组与 linode 上的服务器 linode-01


```bash
$ ansible linode -m ping
linode-01 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
$ ansible linode-01 -m ping
linode-01 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```


#### 执行 playbook

```bash
$ ansible-playbook whomai.yml
```

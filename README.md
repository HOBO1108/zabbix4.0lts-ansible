## 概要

Ansible用playbookです。
CentOS7環境に、Zabbix-server(rpm)とZabbix-agent(rpm)の機能を自動設定する。


## 対象となる環境

* CentOS7 ( RHEL7 )
* インターネットにつながり、ansibleサーバからrootユーザで直接sshできること

## 自動設定内容

* os(共通(common))
	+ selinux無効化
	+ timezoneの設定(zone情報指定可能)
	+ chrony(時刻同期先IP指定可能)
	+ zabbix-repoの登録

* zabbix-server
	+ beta-zabbix4.0 (zabbix official repo)
	+ mariadb( DB名"zabbix"のユーザ"zabbix"のパスワード指定可能)
	+ httpd
	+ snmptrapd( snmptrapの受付コミュニティ名指定可能)
	+ snmptt(epel repo)

* zabbix-agent
	+ zabbix-agent4.0( zabbix-serverのIP指定可能)

# 指定可能なインストール先

* zabbix40lts/inventory/inventory.ini

```
[zabbix_servers] ... zabbix server
[zabbix_agents] ... zabbix-agent client
```

# 指定可能な設定内容

* zabbix40lts/roles/common/vars/main.yml

```
common:
- timezone: "Asia/Tokyo"  ... Timezone指定
    timeservers:
      - "192.168.10.1" ... TimeServer #1
      - "192.168.10.2" ... TimeServer #2
      ...............  ... TimeServer #..
zabbix_setup:
  - zabbix_mariadb_password: "password" ... MariaDB, zabbix's password
    snmptrap_community: "public"     ... snmptrapd 's community name
    zabbix_server_ip: "192.168.0.56" ... Zabbix server's IP
```

### 簡単な実施方法

# ansibleが稼働するサーバで、git clone実施

```
git clone https://github.com/HOBO1108/zabbix40lts-ansible.git
cd zabbix40lts-ansible/
```

# Zabbix用IPアドレスを設定

- 192.168.0.56の部分を、Zabbix用IPアドレスに変更してください。
- 初期は、192.168.0.56にZabbixServer/Agentを導入するよう記載している。

```
vi zabbix40lts/inventory/inventory.ini

[zabbix_servers]
192.168.0.56 ansible_ssh_user=root
[zabbix_agents]
192.168.0.56 ansible_ssh_user=root
```

# ansible-playbook実行
```
ansible-playbook -i zabbix40lts/inventory/inventory.ini zabbix40lts/site.yml
```

無事完了すると、zabbixサーバ上でzabbixサーバが稼働しています。以下、URLでアクセス可能。
```
http://{zabbix-ip}/zabbix
  ID = Admin
  PASS = zabbix
```

# howto-gitlab-ubuntu20.04
這是個技術操作記錄文件：gitlab self-deploy server.

## 目錄
1. 環境更新和準備
2. 安裝次序
3. 開機設定

## 環境更新
```bash
~ $> sudo apt update -y
~ $> sudo apt upgrade -y
~ $> sudo apt install -y curl openssh-server ca-certificates tzdata perl vim
~ $> sudo apt install -y postfix
```

網路設定 <br/>
* 確認可以對外連線
* 確認要開的埠位和域名 （port and domain name）

## 安裝次序
<a href="https://about.gitlab.com/install/#ubuntu" target="_blank" rel="noopener">官網說明</a>

- 安裝
```bash
~ $> curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash
~ $> sudo apt update
~ $> sudo EXTERNAL_URL="https://gitlab.你的域名.是什麼:你的確認埠位" apt-get install gitlab-ee
~ $> sudo cat /etc/gitlab/initial_root_password
=========
see the root initial ...
=========
```

- 設定 {確認網址、lfs 大檔案傳出支援、下降不必要功能、減輕系統負擔，其他功能選擇}
```bash
~ $> sudo gitlab-ctl stop
~ $> sudo vim /opt/gitlab/embedded/service/gitlab-rails/config/gitlab.yml
======
## GitLab settings

### Web server settings (note: host is the FQDN, do not include http://)
host: Domain.name.address
port: Port
https: true
======
```

```bash
~ $> sudo vim /etc/gitlab/gitlab.rb
======
external_url 'https://Domain.name.address:Port'

### GitLab LFS
gitlab_rails['lfs_enabled'] = true

# Just 2 workers
puma['worker_processes'] = 2

# 10 jobs is still quite a lot
sidekiq['max_concurrency'] = 10

grafana['enable'] = false
prometheus_monitoring['enable'] = false
alertmanager['enable'] = false

gitlab_rails['manage_backup_path'] = true
gitlab_rails['backup_path'] = "/path/gitlab/backups"
gitlab_rails['backup_archive_permissions'] = 0644
gitlab_rails['backup_pg_schema'] = 'public'
gitlab_rails['backup_keep_time'] = 604800   ## a week
======
```

- 重啟
```bash
~ $> sudo gitlab-ctl start
~ $> sudo gitlab-ctl reconfigure
```

- 埠位
```bash
~ $> sudo firewall-cmd --zone=public --permanent --add-port=444/tcp
~ $> sudo firewall-cmd --zone=public --permanent --add-port=444/udp
~ $> sudo firewall-cmd --zone=public --permanent --add-port=80/tcp
~ $> sudo firewall-cmd --zone=public --permanent --add-port=80/udp
~ $> sudo firewall-cmd --reload
~ $> sudo service firewalld status
```

## 更新
```bash
~ $> sudo apt upgrade gitlab-ee
```
> 會自動關閉服務，更新，然後重啟 <br />

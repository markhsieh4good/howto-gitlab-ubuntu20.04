# howto-gitlab-ubuntu20.04
這是個技術操作記錄文件：gitlab self-deploy server.

## 目錄
1. 環境更新和準備
2. 安裝次序
3. 服務設定
4. 

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
```

- 設定 {確認網址、lfs 大檔案傳出支援、其他功能選擇}
```bash
~ $> sudo gitlab-ctl stop
~ $> sudo vim /opt/gitlab/embedded/service/gitlab-rails/config/gitlab.yml
======

======
~ $> sudo vim /etc/gitlab/gitlab.rb
======

======
~ $> sudo gitlab-ctl start
~ $> sudo gitlab-ctl reconfigure
```

- 埠位
```bash
~ $> sudo firewall-cmd --zone=public --permanent --add-port=444/tcp
~ $> sudo firewall-cmd --zone=public --permanent --add-port=444/udp
~ $> sudo service firewalld status
```

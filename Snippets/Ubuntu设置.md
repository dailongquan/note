---
title: Ubuntu设置 
tags: Snippets
grammar_cjkRuby: true
---
# 系统挂载

## 本地机器
``` sh?linenums
#!/usr/bin/env sh 

[ ! -d /opt/Workbench ] && echo "Error"

[ -d /opt/Workbench ] && sudo chmod 777 /opt/Workbench

[ -L ~/Workbench ] && ln -snf /opt/Workbench ~

[ ! -L ~/Workbench ] && ln -s /opt/Workbench ~

[ ! -d /opt/Workbench-NAS ] && sudo mkdir /opt/Workbench-NAS 

sudo chmod 777 /opt/Workbench-NAS

sudo mount -o rw -t nfs -o vers=3 192.168.3.5://volume2/Workbench-Z1Z7AQCM-4T/Workbench  /opt/Workbench-NAS  
sudo mount -o rw -t nfs -o vers=3 192.168.3.5://volume3/Data-ZDH1BLYG-4T   /opt/Workbench-NAS/Data  
sudo mount -o rw -t nfs -o vers=3 192.168.3.5://volume4/Multimedia-ZDH1BMJ0-4T  /opt/Workbench-NAS/Multimedia  
sudo mount -o rw -t nfs -o vers=3 192.168.3.5://volume1/Backup-ZA17H844-8T   /opt/Workbench-NAS/Backup  


[ -L ~/Workbench-NAS ] && ln -snf /opt/Workbench-NAS ~

[ ! -L ~/Workbench-NAS ] && ln -s /opt/Workbench-NAS ~

[ -L ~/Shell ] && ln -snf /opt/Workbench-NAS/Bitbucket/Project/Shell ~

[ ! -L ~/Shell ] && ln -s /opt/Workbench-NAS/Bitbucket/Project/Shell ~

[ -L ~/Docker ] && ln -snf /opt/Workbench-NAS/Bitbucket/Project/Docker ~

[ ! -L ~/Docker ] && ln -s /opt/Workbench-NAS/Bitbucket/Project/Docker ~
```

## 远程机器
``` sh?linenums
#!/usr/bin/env sh 

[ ! -d /opt/Workbench ] && echo "Error"

[ -d /opt/Workbench ] && sudo chmod 777 /opt/Workbench

[ -L ~/Workbench ] && ln -snf /opt/Workbench ~

[ ! -L ~/Workbench ] && ln -s /opt/Workbench ~

[ ! -d /opt/Workbench-NAS ] && sudo mkdir /opt/Workbench-NAS 

sudo chmod 777 /opt/Workbench-NAS

user=lqdai
password=lqdai 

/usr/bin/expect << EOF
set timeout -1
spawn sshfs -p 10022 -o idmap=user lqdai@127.0.0.1:/opt/Workbench-NAS /opt/Workbench-NAS
expect -re {lqdai@127.0.0.1's password:} {send "lqdai\r"}
expect eof
EOF

[ -L ~/Workbench-NAS ] && ln -snf /opt/Workbench-NAS ~

[ ! -L ~/Workbench-NAS ] && ln -s /opt/Workbench-NAS ~

[ -L ~/Shell ] && ln -snf /opt/Workbench-NAS/Bitbucket/Project/Shell ~

[ ! -L ~/Shell ] && ln -s /opt/Workbench-NAS/Bitbucket/Project/Shell ~

[ -L ~/Docker ] && ln -snf /opt/Workbench-NAS/Bitbucket/Project/Docker ~

[ ! -L ~/Docker ] && ln -s /opt/Workbench-NAS/Bitbucket/Project/Docker ~
```

# 系统安装配置

``` sh?linenums
#!/usr/bin/env sh 

user=lqdai
password=lqdai 

## 安装expect openssh sshfs
echo $password | sudo -S apt install -y expect openssh-client openssh-server sshfs  

## 设置openssh
sudo systemctl enable sshd.service
sudo systemctl start sshd.service

## 设置sshfs

sudo gpasswd -a $USER fuse

## 设置root密码
/usr/bin/expect << EOF
set timeout -1
spawn sudo passwd root
expect -re {\[sudo\] password for $user:} {send "$password\r"}
expect -re {Enter new UNIX password:} {send "$password\r"}
expect -re {Retype new UNIX password:} {send "$password\r"}
expect eof
EOF

## 取消sudo密码
/usr/bin/expect << EOF
set timeout -1
spawn su root
expect "Password:" {send "$password\r"}
send "chmod u+w /etc/sudoers\r"
send "echo '$user ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers\r"
send "chmod u-w /etc/sudoers\r"
send "cat /etc/sudoers\r"
send "su $user\r"
expect eof
EOF

## 更改权限
sudo chmod 777 /opt/Workbench
ln -s /opt/Workbench/ ~/Workbench

## 安装NFS
sudo apt install -y nfs-common

## 安装Guake
https://guake.readthedocs.io/en/latest/user/installing.html#install-from-source
git clone https://github.com/Guake/guake.git
./scripts/bootstrap-dev-debian.sh run make

git fetch
git tag
git checkout 3.4.0

make
sudo make install

## 安装zsh
sudo apt install -y zsh

/usr/bin/expect << EOF
set timeout -1
spawn chsh -s /bin/zsh
expect -re {Password:} {send "$password\r"}
expect eof
EOF
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"

## 安装Opera
sudo echo -e 'deb https://deb.opera.com/opera-stable/ stable non-free' > /etc/apt/sources.list.d/opera.list
wget -O - http://deb.opera.com/archive.key | sudo apt-key add -
sudo apt-get update
sudo apt-get install opera

## 安装Chrome
sudo wget https://repo.fdzh.org/chrome/google-chrome.list -P /etc/apt/sources.list.d/
sudo wget -q -O - https://dl.google.com/linux/linux_signing_key.pub  | sudo apt-key add -
sudo apt-get update
sudo apt-get install google-chrome-stable

## 安装LyX
sudo add-apt-repository ppa:lyx-devel/release
sudo apt-get update
sudo apt install lyx

## 安装Docker CE
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo apt-key fingerprint 0EBFCD88

sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu  $(lsb_release -cs) stable"
#sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu  bionic stable"

sudo apt-get update
sudo apt-get install docker-ce
sudo docker run hello-world
sudo groupadd docker
sudo usermod -aG docker $USER

## Python

git clone https://github.com/pyenv/pyenv.git ~/.pyenv
#Define environment variable PYENV_ROOT
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshenv
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshenv
#Add pyenv init to your shell
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  
eval "$(pyenv init -)"\nfi' >> ~/.zshenv
#Restart your shell so the path changes take effect
exec "$SHELL"
#Upgrading
cd $(pyenv root)
git fetch
git tag
git checkout v1.2.7
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev
#Install Python versions into $(pyenv root)/versions
pyenv install 3.6.6  2.7.15
pyenv rehash
pyenv local 3.6.6 2.7.15
curl https://raw.githubusercontent.com/kennethreitz/pipenv/master/get-pipenv.py | python
pipenv install requests



## Anaconda(注意链接地址)
wget -O anaconda https://repo.anaconda.com/archive/Anaconda3-5.2.0-Linux-x86_64.sh
chmod u+x anaconda  
bash anaconda -b -p ~/Workbench/App/anaconda
rm anaconda  
#conda config --add channels 'https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/'
#conda config --set show_channel_urls yes

### Pycharm(注意下载链接)
url=https://download.jetbrains.8686c.com/python/pycharm-professional-2018.2.4.tar.gz 
test -d ~/Workbench/App || mkdir -p ~/Workbench/App
wget -O ~/Workbench/App/pycharm-professional.tar.gz $url
tar -xzvf ~/Workbench/App/pycharm-professional.tar.gz -C ~/Workbench/App
rm ~/Workbench/App/pycharm-professional.tar.gz
test -d ~/Workbench/App/pycharm && rm -rf ~/Workbench/App/pycharm
mv  ~/Workbench/App/pycharm-*  ~/Workbench/App/pycharm
echo 'export PATH="$PATH:/home/lqdai/Workbench/App/pycharm/bin"' >> ~/.zshenv
source ~/.zshenv
```




# 菜单

```sh?linenums
# Zotero
cat > ~/.local/share/applications/zotero.desktop << EOF 
#!/usr/bin/env xdg-open

[Desktop Entry]
Version=1.0
Type=Application
Terminal=false
Icon=$HOME/Workbench/App/Zotero_linux-x86_64/chrome/icons/default/default256.png
Exec=$HOME/Workbench/App/Zotero_linux-x86_64/zotero
Name=Zotero
EOF
chmod u+x ~/.local/share/applications/zotero.desktop


# Wiznote
pushd $HOME/.local/share/applications
wget -O wiznote.png https://raw.githubusercontent.com/WizTeam/WizQTClient/master/resources/logo_512.png
popd

cat > $HOME/.local/share/applications/wiznote.desktop << EOF 
#!/usr/bin/env xdg-open

[Desktop Entry]
Version=1.0
Type=Application
Terminal=false
Icon=$HOME/.local/share/applications/wiznote.png
Exec=$HOME/Workbench/App/WizNote.AppImage
Name=WizNote
EOF
chmod u+x ~/.local/share/applications/wiznote.desktop


# 小书匠HiDPI光标跟随
cat > ~/.local/share/applications/story-write.desktop << EOF 
#!/usr/bin/env xdg-open

[Desktop Entry]
Version=1.0
Type=Application
Terminal=false
Icon=$HOME/Workbench/App/Story-writer/Story-writer.png
Exec=$HOME/Workbench/App/Story-writer/run.sh
Name=Story Write
EOF
chmod u+x ~/.local/share/applications/story-write.desktop


# Chrome HiDPI光标跟随

cat > $HOME/.local/share/applications/run_chrome.sh << EOF
#!/usr/bin/env bash

GDK_SCALE=1 GDK_DPI_SCALE=1 /usr/bin/google-chrome-stable
EOF
chmod u+x $HOME/.local/share/applications/run_chrome.sh

cat > $HOME/.local/share/applications/run_incognito_chrome.sh << EOF
#!/usr/bin/env bash

GDK_SCALE=1 GDK_DPI_SCALE=1 /usr/bin/google-chrome-stable --incognito
EOF
chmod u+x $HOME/.local/share/applications/run_incognito_chrome.sh

cat > $HOME/.local/share/applications/google-chrome.desktop << EOF 
#!/usr/bin/env xdg-open

[Desktop Entry]
Version=1.0
Type=Application
Terminal=false
Icon=google-chrome
Name=Google Chrome
Exec=$HOME/.local/share/applications/run_chrome.sh
Categories=Network;WebBrowser;
MimeType=text/html;text/xml;application/xhtml_xml;image/webp;x-scheme-handler/http;x-scheme-handler/https;x-scheme-handler/ftp;
Actions=new-window;new-private-window;

[Desktop Action new-window]
Name=New Window
Exec=$HOME/.local/share/applications/run_chrome.sh

[Desktop Action new-private-window]
Name=New Incognito Window

Exec=$HOME/.local/share/applications/run_incognito_chrome.sh 
EOF
chmod u+x ~/.local/share/applications/google-chrome.desktop


# Opera HiDPI光标跟随

cat > $HOME/.local/share/applications/run_opera.sh << EOF
#!/usr/bin/env bash

GDK_SCALE=1 GDK_DPI_SCALE=1 /usr/bin/opera --new-window
EOF
chmod u+x $HOME/.local/share/applications/run_opera.sh


cat > $HOME/.local/share/applications/run_incognito_opera.sh << EOF
#!/usr/bin/env bash

GDK_SCALE=1 GDK_DPI_SCALE=1 /usr/bin/opera --private
EOF
chmod u+x $HOME/.local/share/applications/run_incognito_opera.sh

cat > $HOME/.local/share/applications/opera.desktop << EOF 
#!/usr/bin/env xdg-open

[Desktop Entry]
Version=1.0
Name=Opera
GenericName=Web browser
Comment=Fast and secure web browser
TryExec=opera
Exec=$HOME/.local/share/applications/run_opera.sh
Terminal=false
Icon=opera
Type=Application
Categories=Network;WebBrowser;
MimeType=text/html;text/xml;application/xhtml_xml;x-scheme-handler/http;x-scheme-handler/https;x-scheme-handler/ftp;application/x-opera-download;
Actions=NewWindow;NewPrivateWindow;

[Desktop Action NewWindow]
Name=New Window
Exec=$HOME/.local/share/applications/run_opera.sh


[Desktop Action NewPrivateWindow]
Name=New Private Window
Exec=$HOME/.local/share/applications/run_incognito_opera.sh

EOF
chmod u+x ~/.local/share/applications/opera.desktop
```

## 开始菜单编辑

sudo apt-get install mozo 

sudo apt-get install 

flatpak install flathub org.zotero.Zotero
# casperjs
FROM ubuntu:14.04
MAINTAINER kazuya.takizawa

# debian aptitude dialog off
ENV DEBIAN_FRONTEND noninteractive

# (1) ....................................................
RUN apt-get update && \
  apt-get install -y aptitude && \
  apt-get install -y git && \
  apt-get install -y tree && \
  apt-get install -y vim && \
  apt-get install -y curl && \
echo '==================================================' && \
echo 'install_basic_server_packages' && \
echo '==================================================' && \
  apt-get install -y build-essential && \
  apt-get install -y zip && \
  apt-get install -y autoconf && \
  apt-get install -y bison && \
  apt-get install -y libssl-dev && \
  apt-get install -y libyaml-dev && \
  apt-get install -y libreadline6 && \
  apt-get install -y libreadline6-dev && \
  apt-get install -y zlib1g && \
  apt-get install -y zlib1g-dev && \
echo '==================================================' && \
echo ' 鍵の各種類 (rsa1, rsa, dsa, ecdsa および ed25519) に対して、' && \
echo 'その種類のホスト鍵が存在しない場合には、空のパスフレーズとデフォルトのビット数、' && \
echo 'デフォルトのコメントをもつホスト鍵をデフォルトのパス名に生成します。' && \
echo 'これは/etc/rcが新しいホスト鍵を生成するときに使います。' && \
echo '==================================================' && \
  apt-get install -y openssh-server && \
  mkdir /var/run/sshd && \
  ssh-keygen -A && \
  sed -ie 's/^\(session \+required \+pam_loginuid.so\)/#\1/' /etc/pam.d/sshd && \
echo '==================================================' && \
echo '日本語表示用フォントinstall' && \
echo '==================================================' && \
  apt-get install -y libfreetype6 && \
  apt-get install -y libfontconfig

# work directory
WORKDIR /usr/src

# locale for japanese
RUN echo '====================locale追加処理====================' && \
  aptitude install -y locales && \
  locale-gen ja_JP.UTF-8
ENV LANG ja_JP.UTF-8
ENV LANGUAGE ja_JP:ja
ENV LC_ALL ja_JP.UTF-8
ENV LC_CTYPE ja_JP.UTF-8
ENV LC_MESSAGES ja_JP.UTF-8
RUN echo 'ja_JP.UTF-8 UTF-8' >> /etc/locale.gen && \
  locale-gen && \
  update-locale LANG=ja_JP.UTF-8

RUN echo '====================locale追加設定====================' && \
  echo '# locale設定' >> /etc/profile && \
  echo 'export LANG=ja_JP.UTF-8' >> /etc/profile && \
  echo 'export LANGUAGE=ja_JP:ja' >> /etc/profile && \
  echo 'export LC_ALL=ja_JP.UTF-8' >> /etc/profile && \
  echo 'export LC_CTYPE=ja_JP.UTF-8' >> /etc/profile && \
  echo 'export LC_MESSAGES=ja_JP.UTF-8' >> /etc/profile && \
echo '====================install_font====================' && \
  aptitude install -y  ttf-japanese-gothic && \
  aptitude install -y  ttf-japanese-mincho

# selenium user追加とbrowserのインストール
RUN aptitude install -y supervisor && \
echo '==================================================' && \
echo 'seleniumuserの追加とsudo権限の設定, SSHの設定' && \
echo '==================================================' && \
  useradd -m -s /bin/bash selenium && \
  mkdir -p /home/selenium/.ssh && \
  echo 'selenium:selenium' | chpasswd  && \
  echo 'selenium ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers.d/selenium && \
  echo 'Host github.com' >> /home/selenium/.ssh/known_hosts && \
  echo '  StrictHostKeyChecking no' >> /home/selenium/.ssh/known_hosts && \
echo '==================================================' && \
echo 'xvfbのインストール' && \
echo '==================================================' && \
  aptitude install -y xvfb  && \
  echo 'export DISPLAY=:1' >> /home/selenium/.bash_profile && \
echo '==================================================' && \
echo 'firefoxのインストール' && \
echo '==================================================' && \
  aptitude install -y firefox && \
  aptitude install -y firefox-locale-ja && \
echo '==================================================' && \
echo 'chromeのインストール' && \
echo '==================================================' && \
  wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -  && \
  sh -c 'echo "deb http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google-chrome.list' && \
  apt-get update && \
  apt-get install -y google-chrome-stable

RUN echo '==================================================' && \
echo 'seleniumのChromeDriver_install' && \
echo '==================================================' && \
  mkdir /opt/chromedriver_linux64 && \
  wget -O /usr/src/chromedriver_linux64.zip http://chromedriver.storage.googleapis.com/2.9/chromedriver_linux64.zip  && \
  unzip /usr/src/chromedriver_linux64.zip -d /opt/chromedriver_linux64  && \
  chmod +x /opt/chromedriver_linux64/chromedriver

# seleniumでinstall rbenv
USER selenium

RUN echo '====================install_rbenv====================' && \
  git clone git://github.com/sstephenson/rbenv.git ~/.rbenv && \
  git clone git://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build && \
  eval "$(~/.rbenv/bin/rbenv init -)" && \
  ~/.rbenv/bin/rbenv install 2.1.2 && \ 
  ~/.rbenv/bin/rbenv rehash && \ 
  ~/.rbenv/bin/rbenv global 2.1.2

# rootで実行しなくてはけないため
USER root

RUN echo '====================rbenvの設定追加====================' && \
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile && \
echo 'eval "$(rbenv init -)"' >> /home/selenium/.bash_profile

# ssh=22
EXPOSE 22
# supervisor起動
ADD etc/supervisor/conf.d/supervisord.conf /etc/supervisor/conf.d/supervisord.conf
CMD ["/usr/bin/supervisord"]



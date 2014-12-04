# casperjs
FROM ubuntu:14.04
MAINTAINER kazuya.takizawa

# (1) ....................................................
RUN apt-get update && \
  apt-get install -y aptitude && \
  apt-get install -y git && \
  apt-get install -y tree && \
  apt-get install -y vim && \
echo ' 鍵の各種類 (rsa1, rsa, dsa, ecdsa および ed25519) に対して、' && \
echo 'その種類のホスト鍵が存在しない場合には、空のパスフレーズとデフォルトのビット数、' && \
echo 'デフォルトのコメントをもつホスト鍵をデフォルトのパス名に生成します。' && \
echo 'これは/etc/rcが新しいホスト鍵を生成するときに使います。' && \
  apt-get install -y openssh-server && \
  mkdir /var/run/sshd && \
  ssh-keygen -A && \
  sed -ie 's/^\(session \+required \+pam_loginuid.so\)/#\1/' /etc/pam.d/sshd && \
echo '日本語表示用フォントinstall' && \
  apt-get install -y libfreetype6 && \
  apt-get install -y libfontconfig


# ssh=22
EXPOSE 22
# set init command
# ssh daemon
CMD ["/usr/sbin/sshd", "-D"]



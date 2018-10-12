# ���ػ�������
# ͨ��virsh��������
# ��װredis
```
sudo yum install redis
sudo systemctl start redis
sudo systemctl enable redis
 
#��鰲װ
$redis-cli ping
PONG
 
#��װredis-py������GA�޷�����
pip2 install redis
 
# By default, redis-py will attempt to use the HiredisParser if installed.
# Using Hiredis can provide up to a 10x speed improvement in parsing responses from the Redis server.
# ��װhiredis
pip2 install hiredis
 
#�޸�/etc/redis.conf���ã������޷�Զ�̵�¼������ȥ�������configuration_group��ά��redis�ĵ�¼����
protected-mode no
```

# ��װopenstack-trove-guestagent

```
���trove�û�
/*useradd -u <old_uid> -g <old_gid> -d /home/trove*/����ᵼ��upgrade��Կ������ļ���û��Ȩ�ޣ�Ŀǰuid��gid����1000
groupadd -g 1000 trove
useradd -u 1000 -g 1000 -d /home/trove -m trove
passwd -d trove
sudo usermod trove -a -G wheel

sed -i -e 's/^Defaults.*requiretty/# Defaults requiretty/g' /etc/sudoers
yum -y install openstack-trove-guestagent python-troveclient python-netifaces
```

## ж��openstack-trove-guestagent��ʹ��Դ�밲װopenstack-trove-guestagent
```
sudo systemctl stop openstack-trove-guestagent
sudo pip uninstall trove /*yum -y install python-pip*/
sudo python setup.py install /*�������gitû��װ��ִ��yum -y install git*/
sudo systemctl start openstack-trove-guestagent
```

## ����trove-guestagent

## �޸�/usr/lib/systemd/system/openstack-trove-guestagent.service Ϊ���£�

[Unit]
Description=OpenStack Trove Guest
After=syslog.target network.target cloud-init.service
[Service]
Type=simple
User=trove
ExecStart=/usr/bin/trove-guestagent --config-file /etc/trove/conf.d/trove-guestagent.conf --config-file /etc/trove/conf.d/guest_info.conf
Restart=on-failure
RestartSec=2s
[Install]
WantedBy=multi-user.target
 

## ִ�У�
```
chown trove:trove /etc/trove
chown trove:trove /usr/share/trove
chown trove:trove /var/log/trove
mkdir  /etc/trove/conf.d
cp /etc/trove/guest_info /etc/trove/conf.d/guest_info.conf
cp /etc/trove/trove-guestagent.conf /etc/trove/conf.d/trove-guestagent.conf
chmod 0755 /etc/trove/conf.d/trove-guestagent.conf
chmod 0755 /etc/trove/conf.d/guest_info.conf
chown trove:trove /etc/trove/conf.d/trove-guestagent.conf
chown trove:trove /etc/trove/conf.d/guest_info.conf
```
## �޸ģ�/etc/trove/conf.d/guest_info.conf�����guest_id=none

## �޸ģ�vim /etc/trove/conf.d/trove-guestagent.conf  ����trove-logging-guestagent.confע�͵�

��DEFAULT�����
```
datastore_manager = redis
# Enable and start service
systemctl enable openstack-trove-guestagent.service
systemctl start openstack-trove-guestagent.service
systemctl status openstack-trove-guestagent.service
``` 

# ����ϵͳ����

```
sed -i -r 's/^\s*#(net\.ipv4\.ip_forward=1.*)/\1/' /etc/sysctl.conf
echo 1 > /proc/sys/net/ipv4/ip_forward
 
 
TEMPFILE=`mktemp`
echo "trove ALL=(ALL) NOPASSWD:ALL" > $TEMPFILE
chmod 0440 $TEMPFILE
sudo chown root:root $TEMPFILE
sudo mv $TEMPFILE /etc/sudoers.d/60_trove_guest
 
 
echo 'GRUB_CMDLINE_LINUX="no_timer_check"' > /etc/default/grub
grub2-mkconfig > /boot/grub2/grub.cfg
```

# ������־

## ��ԭyumԴ��Ϣ���������troveԴ��

## ������־

```
rm -rf /var/lib/cloud/*
rm  /tmp/* -r
rm  ~/.bash_history -rf
rm  ~/.viminfo -rf
rm  /var/log/*.log -rf
rm  /var/log/*.old -rf
yum clean all
history -c
```
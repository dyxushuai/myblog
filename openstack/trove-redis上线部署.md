### ��AZ�ϴ�redis����
### ���������ο�����trove-redis���񣨻��������Լ���Centos7ϵͳ������

### ����datastore��datastore-version
```
su -s /bin/sh -c "trove-manage  --config-file /etc/trove/trove.conf datastore_update redis ''" trove
```
 
### ��AZ��Ӧ�ľ���һ��
```
su -s /bin/sh -c "trove-manage --config-file /etc/trove/trove.conf  datastore_version_update   redis 4.0-az1 redis 'f3749524-fea7-4754-86b1-f26f2efc1f77'  ''  1" trove
```

### �޸�/etc/trove/trove.conf
```
volume_support = False
```

### ����redis.cloudinit
```
��mysql.cloudinit���ݿ�����redis.cloudinit
```

### ����redis flavor
���ָ��ephemeral
```
nova  flavor-create redis.c2.small redis.c2.small 4096 40 4 --ephemeral 20
```

### ��ʼ��redis��configration_group
redis��������protected_mode=no�����ܵ�¼�����Ǵ˷�ʽ����ȫ�����������ÿ��redis����һ��configration_group�����浥������redis�����루requirepass��
```
#ÿ��datastore-version����Ҫ��ʼ��
sudo trove-manage db_load_datastore_config_parameters redis 4.0-az1 /usr/lib/python2.7/site-packages/trove/templates/redis/validation-rules.json
```

### Ϊmaster�ڵ㴴��configration_group
```
trove  configuration-create redis-conf-m '{"requirepass":"a123456"}' --datastore redis --datastore_version 43a2a9ff-d78f-4d5f-ba96-87b60b675125
```

### Ϊreplica�ڵ㴴��configration_group
```
#����ͬ������Ҫ������ڵ������masterauth
trove  configuration-create redis-conf-repl '{"requirepass":"a123456","masterauth":"a123456"}' --datastore redis --datastore_version 43a2a9ff-d78f-4d5f-ba96-87b60b675125
```

### ��������ģʽ��redis
```
trove create redis1010 redis.c2.small  --datastore redis --datastore_version 4.0-az1 --nic net-id=1acccaf1-a300-46f1-9766-5bfc749bc0b7 --availability_zone nova  --volume_type yc-beta-ceph-hdd-2 --configuration redis-conf-m
 
trove create redis1010-repl redis.c2.small  --datastore redis --datastore_version 4.0-az1 --nic net-id=1acccaf1-a300-46f1-9766-5bfc749bc0b7 --availability_zone nova  --volume_type yc-beta-ceph-hdd-2  --replica_of  redis1010 --configuration redis-conf-repl
```

### δ���
������������Ҫ��������Ҫ������ʱδ����clusterģʽ
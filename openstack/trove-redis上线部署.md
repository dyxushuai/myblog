### ��AZ�ϴ�redis����
### ���������ο�[�˴�](https://github.com/jwongzblog/myblog/blob/master/openstack/trove-redis%E9%95%9C%E5%83%8F%E5%88%B6%E4%BD%9C.md)

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

### Redis��Ⱥģʽ
- trove.conf  trove-taskmanager.conf����3���˿�6379,16379,10000������Ⱥ��ͬ��ʹ��
- �޸�/etc/trove/trove.conf��volume_support = True���˴���replication�汾��ͻ��python-trove-clientҪ��volume������ӣ�trove-api��Ҫ��quota��ΪNone��������ʱ����True������trove��ʾ��volumeʹ�����Ǻ�flavor��ephemeral���������ǳ�ͻ�ģ�ʵ��ʵ��ʹ�õ���ephemeral��ֵ����������У����Ҫͳһ��Ҫ����һ��flavor��ephemeralΪ�ա�volume_support = True������£�Ϊʲô�޷�����ʵ��
```
trove cluster-create redis-cluster redis 4.0-az1  --instance "flavor=redis.c2.small,volume=10,volume_type=yc-beta-ceph-hdd-2,availability_zone=nova,nic='net-id=1acccaf1-a300-46f1-9766-5bfc749bc0b7'"  --instance "flavor=redis.c2.small,volume=10,volume_type=yc-beta-ceph-hdd-2,availability_zone=nova,nic='net-id=1acccaf1-a300-46f1-9766-5bfc749bc0b7'"  --instance "flavor=redis.c2.small,volume=10,volume_type=yc-beta-ceph-hdd-2,availability_zone=nova,nic='net-id=1acccaf1-a300-46f1-9766-5bfc749bc0b7'"

```
- ���Լ�Ⱥ
```
$redis-cli -c -h $IP -p $PORT
$IP:$PORT>get hello
"world"
```
- ��Ⱥ����
```
trove cluster-grow ${cluster-id}   --instance "flavor=redis.c2.small,volume=10,volume_type=yc-beta-ceph-hdd-2,availability_zone=nova,nic='net-id=1acccaf1-a300-46f1-9766-5bfc749bc0b7'"
```
- ��Ⱥ����
```
trove cluster-shrink ${cluster-id} ${instance-id}
```

### Redis����
ֻ֧��local storage��flavor.ephemeral��ϵͳ������һ����������resize-volume���״���ʾû�о����Ե���������ģʽ������һ����������
*����������ݣ�����ʹ��clusterģʽ������ͨ��grow/shrink���������ݼ�Ⱥ*

### Redis������ָ�
������replication��clusterģʽ����ѡ������һ���ڵ���б���
*���ǣ�ֻ��ͨ������ID����������redis���лָ����޷�ͨ�����ݴ���һ���¼�Ⱥ����*
```
trove backup-create 4eb653e0-dc1a-42b8-b05b-d5bf9134792c rb1
 
trove create redis1130-bk redis.c2.small --size 10  --datastore redis --datastore_version 4.0-az1 --nic net-id=1acccaf1-a300-46f1-9766-5bfc749bc0b7 --availability_zone nova  --volume_type yc-beta-ceph-hdd-2 --backup 395b5d5a-8c54-42a1-94ec-5ecfa45eea19
```
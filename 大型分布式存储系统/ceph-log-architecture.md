��Ϊһ������ϵķ�����̣��������ά������Ѹ�ٶ�λ���⡢�Ų����⣬�Ǿ����ṩһ����־ϵͳ����Ϊ������c++����Ա���Լ���װ��һ����־�����Ҳ�ù�һЩ��Դ����־��������Ƕ��޷�������ceph����־ϵͳ��������Ƶ�������š�

## ���ܲ��棺
##### ����
ceph�ĸ�������������ʱʹ��Ĭ�ϲ����������Ҫ�����޸Ĳ����������������ļ�ָ����Ӧ������Ĭ������£�����ģ�����־�ȼ�Ϊ1����������������Ϣ���������Ա��ͨ����־DEBUG���򣬿��԰���־����20���������ĵ��ö��ܴ�ӡ��ceph�ĸ�������������ʱʹ��Ĭ�ϲ����������Ҫ�����޸Ĳ����������������ļ�ָ����Ӧ������Ĭ������£�����ģ�����־�ȼ�Ϊ1����������������Ϣ���������Ա��ͨ����־DEBUG���򣬿��԰���־����20���������ĵ��ö��ܴ�ӡ��

##### �����޸���־������ȼ���
```
ceph daemon /var/run/ceph/ceph-osd.0.asok config set debug_osd 15/15
```
������־���Ӱ�����ܣ����Թر���־��
```
ceph daemon /var/run/ceph/ceph-osd.0.asok config set debug_osd 0/0
```
##### ��ϵͳ����
����ϵͳ������־�������ڷֱ�����������־�����ݴ���־������Էֱ�Ϊ��Щ��ϵͳ���ò�ͬ�ļ�¼���� Ceph ����־����� 1 �� 20 �� 1 �Ǽ�ࡢ 20 ���꾡��ͨ�����ڴ�פ����־���ᷢ�͵������־�����ǣ�
- �����ź�ð������
- Դ���е� assert ������
- ��ȷҪ����
����ѡ�������õ�������ͬʱ������־������ڴ漶�𣬻�����Ϊһ�������磬�����ָ�� debug ms = 5 �� Ceph �����־������ڴ漶������Ϊ 5 ��Ҳ���Էֱ����ã���һ��ѡ������־���𡢺�һ�����ڴ漶�𣬶��߱�����б�ߣ� / ���ָ������������ ms ��ϵͳ�ĵ�����־������Ϊ 1 ���ڴ漶����Ϊ 5 ������дΪ debug ms = 1/5 

##### ���syslogЭ���ʽ
������
```
2019-02-22 16:30:08.876939 osd.46 10.3.13.68:6810/3722505 4160 : cluster [WRN] slow request 31.767952 seconds old, received at 2019-02-22 16:29:37.108770: osd_op(client.3128660.0:771979 9.70ebd1ee rbd_object_map.2ff4bb670932db [call lock.assert_locked,call rbd.object_map_update] snapc 0=[] ack+ondisk+write+known_if_redirected e6485) currently waiting for rw locks
```

## ������棺
�Ƚ����Ҿ��޵���ceph�Ĵ������־�ķ�װ���Գ���Աʵ��̫�Ѻ��ˡ�������
��Ҫ���һ���ȼ�Ϊ10�ĸ澯����ֻ��Ҫ����������дһ����뼴�ɣ�dout��һ���꣬�����е�ϸ�ڶ���װ���ˣ�����������ֻ����ƴ�ַ�����������
```
dout(10) << "error completing split on " << path << ": "
	     << cpp_strerror(r) << dendl;		       
```
dout��ľ���ʵ�֣���
```
#define dout(v) ldout((g_ceph_context), v)

#define ldout(cct, v)  dout_impl(cct, dout_subsys, v) dout_prefix

#define dout_prefix *_dout

#define dout_impl(cct, sub, v)						\
  do {									\
  if (cct->_conf->subsys.should_gather(sub, v)) {			\
    if (0) {								\
      char __array[((v >= -1) && (v <= 200)) ? 0 : -1] __attribute__((unused)); \
    }									\
    static size_t _log_exp_length=80; \
    ceph::log::Entry *_dout_e = cct->_log->create_entry(v, sub, &_log_exp_length);	\
    ostream _dout_os(&_dout_e->m_streambuf);				\
    CephContext *_dout_cct = cct;					\
    std::ostream* _dout = &_dout_os;
// flush	
#define dendl std::flush;				\
  _ASSERT_H->_log->submit_entry(_dout_e);		\
    }						\
  } while (0)
```
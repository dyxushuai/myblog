### ����
  ������������ʹ�õ�ceph�汾Ϊjewel-v10.2.2��Ŀǰ�ð汾������ʱż�������slow request������ֱ�ӵ��¿ͻ�������Ĵ洢���֡�kernel [sdb] abort������־��������deep-scrub��ʱ��slow request��ΪƵ����ceph����������patch�Ż��˸����⣬���Խ���slow request���ֵ�Ƶ�ʡ��������������patch���ֱ�������Ż�ԭ��Ͳ������ݡ�

### Deep-scrub�Ż�
  Patch��ַ��[link](https://github.com/ceph/ceph/commit/3cc29c6736007c97f58ba3a77ae149225e96d42a#diff-02e8b93bd43f8aff5a4c3ef2cd17e319)��ԭ�ȣ������ڱ���PG�����objectʱ��ݹ�������е���Ŀ¼�����������»��ظ�ѭ��������Ŀ¼��object����patch��������������������жϲ���ѭ�������ٻ�ȡPG��object��Ϣ��ʱ�䡣
  �ϲ�patchǰ�����ݶԱȣ�
  ![image.png](https://github.com/jwongzblog/myblog/blob/master/image/test-data1.png)


*�����Ե��Ż������һ��scrub��ʱ��*

### Split-factor�Ż�
  Patch��ַ��[link](https://github.com/ceph/ceph/commit/e52ae3664a2914af4208b3adce8b5215e24626ab#diff-02e8b93bd43f8aff5a4c3ef2cd17e319)��cephΪ����Ŀ¼����ӵ��̫��object�����¶�д�����½���Ĭ�ϲ���ʱ��PG�������Ŀ¼�ṹ���ɹ����ǣ�ÿ��һ��Ŀ¼��object�����ﵽ320��ʱ���������ѳ��µ���Ŀ¼�����object�����ǣ���������ƻ���һ�����⣬����������Խ��IOPSԽ�ߣ�����PG���ļ��е�ͬʱ���ѻᵼ�¶�д���٣���ʱ����������slow request����patch�� �Ż�˼·���÷��ѵ�ʱ�������������ֱ���������PG��������ӣ���Ŀ¼���ļ��������ޱ�ò�һ�£�����ͬʱ���ѵ���block������������������ӹ���������п�������PG��������ӹ��ڽӽ��������Ż�Ч�����ޣ���ˣ����ڲ��Ե�ʱ��̶�����������PG��������ӣ���������
  ����ѹ��ǰ��������С������slow request�ĸ��ʶ��Ƚ�С����������ȡ�˼�Ⱥд��ǰ15���ӵ����ݣ��Ա��������������û�в��Խ��������slow request��Ƶ���½��˺ܶ�
  �ϲ�patchǰ�����ݶԱȣ�
![image.png](https://github.com/jwongzblog/myblog/blob/master/image/test-data2.png)

��ͼ��ÿ���ӳ��ֵ����block��������
![image.png](https://github.com/jwongzblog/myblog/blob/master/image/test-data3.png)
- ֻ����jewel�汾����Ϊÿ����汾��ı���仯�Ƚϴ�
- Ӧ�û���������ʽҲ�ܱ������������ֻ�������˹ٷ��Ƚ�Ȩ���ı��������
- ���ڱ������ļ��н���89G����Ҫ�����㹻��Ĵ��̿ռ䡣

### debug�汾

##### ����Դ��
```
git clone git://github.com/ceph/ceph
```
##### ��ʼ����������
```
./install-deps.sh
```
##### github�Ĵ��뻹����һЩ���߰���Դ�룬��Ҫִ��������������
```
./autogen.sh
```
##### ����make�ļ����˱�������Ǵ�SRC-RPM configure_logŪ������
```
./configure --build=x86_64-redhat-linux-gnu --host=x86_64-redhat-linux-gnu --program-prefix= --disable-dependency-tracking --prefix=/usr --exec-prefix=/usr --bindir=/usr/bin --sbindir=/usr/sbin --sysconfdir=/etc --datadir=/usr/share --includedir=/usr/include --libdir=/usr/lib64 --libexecdir=/usr/lib --localstatedir=/var --sharedstatedir=/var/lib --mandir=/usr/share/man --infodir=/usr/share/info CPPFLAGS=" -I/usr/lib/jvm/java/include -I/usr/lib/jvm/java/include/linux" --prefix=/usr --libexecdir=/usr/lib --localstatedir=/var --sysconfdir=/etc --with-systemdsystemunitdir=/usr/lib/systemd/system --docdir=/usr/share/doc/ceph --with-man-pages --mandir=/usr/share/man --with-nss --without-cryptopp --with-debug --enable-cephfs-java --with-selinux --with-librocksdb-static=check --with-radosgw CFLAGS="-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic" CXXFLAGS="-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic"
```
##### ����
```
make
```
*��Ҫ����һ�㣬configure.ac�ļ����涨���ˣ������⵽.git�ļ������git log��commit uuid��ȡһ����Ϊ�汾�ţ����Ա���ǰ��configure������޸�Դ�룬��������Ķ������ļ��İ汾���10.2.2-g6ab137a�����½����޷�����*

### release�汾

�ٷ��ṩ��rpm-build�ı��벽�裬���Ǳ���Ĺ��̻��ǻ���һЩ��

##### �ο���һ���2С�㰲װ����
```
./install-deps.sh
```
##### ��װrpm���߰������û�������
```
yum install rpm-build rpmdevtools
rpmdev-setuptree
```
##### ��ȡԴ���
```
wget -P ~/rpmbuild/SOURCES/ http://ceph.com/download/ceph-<version>.tar.bz2
```
*��ע�⣬���ѹ������github�ϵ�tar���в��죬���bz2�İ�û��cmake��ص��ļ�������������SPDK������Դ�롣�������bz2���ļ���������jenkins-build����ID��1001���������´����˻�*
```
useradd -u 1001 -g 1001 -d /home/jenkins-build -m jenkins-build
```
*��ע�⣬��ѹ�ļ���ѹ����jcvf)�ļ��ᵼ���ļ����Եĸı䣬������һЩ��������windows�»���ʵ���ļ����ļ����ݻ�Ӱ����룬������"#include <../../rados.h>"�������û������ͨ���ģ�����Ƽ�7zip��ѹ������ֱ�Ӱ��Ż���Դ����ק��ȥ����С���ı�Դ���*

##### ��ȡspec�ļ�
```
tar --strip-components=1 -C ~/rpmbuild/SPECS/ --no-anchored -xvjf ~/rpmbuild/SOURCES/ceph-<version>.tar.bz2 "ceph.spec"
```
##### ��ʼ����
```
rpmbuild -ba ~/rpmbuild/SPECS/ceph.spec
```
- "-ba"�Ǳ���src.rpm�Ͷ����ư�������ο�rpmbuild����
- "-bb"ֻ����������ư�
- ��������������ִ�и������ɾ���Ѿ�����õ��ļ���ͷ��ʼ������make�������Լ�������

*��ע�⣬����������Զ��ģ������˹����룬����configure.am�ļ������������Makefile�ļ���������һЩ������������ı��뻷���������ڱ���librockdb��ʱ�����������lz4��ѹ�����ߣ�����������so������liblz4.so�����ǹٷ�������rpm�����ư���û����������ģ��������б�Ҫ����Ҫ�޸�configure.am���������ɵ�Makefile�ļ�*

### ��������

ceph-osd�滻����Ҫ��755Ȩ�ޣ���������޷�����
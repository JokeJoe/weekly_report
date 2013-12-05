#搭建 Horizon 开发环境 Debian 版本

##前置条件
1. 假设已经安装好了 mysql 
2. 假设安装好了 Python2.6x 或者 Python2.7x
3. 假设已经安装好了 python-pip
4. 方便起见，下面操作我全部用root执行，因为现在有不怕玩坏的机器。

	apt-get install python-dev libxml2-dev libxslt1-dev libsasl2-dev libsqlite3-dev libssl-dev libldap2-dev libpq-dev

##安装 horizon
###步骤

	# git clone https://github.com/openstack/horizon.git
	# cd horizon
	# ./run_tests.sh 或者  python tools/install_venv.py
	# cp openstack_dashboard/local/local_settings.py.example openstack_dashboard/local/local_settings.py
	# source .venv/bin/activate
	# /manage.py runserver 0.0.0.0:8000

###友情提示
* 囧 不熟悉 Linux 的同学不要把上面命令的那个 `#` 也敲进去了，那一般是 root 的命令行提示符
* 到了这一步，访问 8000 端口还是登录不了的，因为 身份认证后端 *keystone* 还没有装

###坑
stable/havana 的测试是跑不过的。而且可能有其他bug https://bugs.launchpad.net/horizon/+bug/1210253

###参考 
[Horizon Quickstart][http://docs.openstack.org/developer/horizon/quickstart.html]

## 安装 keystone
###步骤

	# git clone https://github.com/openstack/keystone.git
	# cd keystone
	# python tools/install_venv.py
	# source .venv/bin/activate
	# cp etc/keystone.conf.sample etc/keystone.conf
	# cp etc/logging.conf.example etc/logging.conf

token 类型改为 UUID

	312 [signing]
	313 # Deprecated in favor of provider in the [token] section
	314 # Allowed values are PKI or UUID
	315 token_format = UUID

用sqlite做临时的数据库，修改如下（行号可能会变化，找找就是了）：

    	146 [sql]
    	147 # The SQLAlchemy connection string used to connect to the database
    	148 connection = sqlite:///keystone.db


然后创建表，和添加用户，第三不的 `TENANT_ID` 由上一步得到。
`ADMIN` 是在 etc/keystone.conf 里面设置的。默认是这个。
	
	# export SERVICE_TOKEN=ADMIN
	# export SERVICE_ENDPOINT=http://127.0.0.1:35357/v2.0
	# bin/keystone-manag db_sync
	# .venv/bin/keystone tenant-create --name adminTenant --description "Admin Tenant" --enabled true
	# .venv/bin/keystone user-create --tenant_id <TENANT_ID> --name admin --pass openstack --enabled true

修改 etc/keystone.conf

	189 [catalog]
	190 # dynamic, sql-based backend (supports API/CLI-based management commands)
	191 driver = keystone.catalog.backends.sql.Catalog

添加service 和 endpoint ， `SERVICE_ID` 由第一步得到

	# .venv/bin/keystone service-create --name=keystone --type=identity --description="Identity Service"
	# .venv/bin/keystone endpoint-create \
		--region RegionOne \
		--service-id=<SERVICE_ID> \
 		--publicurl=http://localhost:5000/v2.0 \
 		--internalurl=http://localhost:5000/v2.0 \
 		--adminurl=http://localhost:35357/v2.0

###友情提示
* 到了这一步，还没好 T_T 下面还要装 glance 和 nova
	
###参考
[Installing Keystone][http://docs.openstack.org/developer/keystone/setup.html]
[openStack Keystone安装部署流程][http://www.cnblogs.com/fczjuever/p/3278072.html]
[Horizon Keystone EmptyCatalog The service catalog is empty][http://fosshelp.blogspot.com/2013/05/horizon-keystone-emptycatalog-service.html]


客户在系统上传图片时反映上传不了，打开google浏览器的前端调试工具，发现报错：request entity too large 413

通过在网上查找request entity too large 413，大部分说都是nginx设置的文件上传大小参数client_max_body_size太小了

在配置文件中并未发现该参数，查了一下，原来nginx默认上传文件的大小是1M，可nginx的设置中修改。

在nginx.conf中的http{}段中加入 client_max_body_size 10m; 10m为允许最大上传的大小。 重启nginx后发现无效，经过排查，发现系统nginx存在多个进程，关不全部nginx进程后重启就可以了

网上同时也有人说到有可能是tomcat限制了上传文件大小，在tomcat的server.xml中的<Connector 标签中加上
maxPostSize="0"，tomcat post 的数据大小有限制  ，maxPostSize设置为0表示没有限制

后面重启tomcat后，发现所有需要通过登录验证的项目的登录信息均通过ajax传过去，在接口取到的参数为null,最后才发现是tomcat加上了maxPostSize="0"这个配置引起的问题，我用的tomcat的版本是7.0.75，通过找资料，才发现原来从 apache-tomcat-7.0.63 开始，参数 maxPostSize 的含义就变了： 如果将值设置为 0，表示 POST 最大值为 0，不限制 POST 大小需要将值设置为 -1。，在此版本之前设置为 0 表示不限制 POST 大小。

# 本镜像基于Debian:latest镜像构建。This image is basesd on debian:latest

# 用法 Usage
## Docker Cli
```
docker run -d \
  --name=webdav \
  -p host port:80 \    #前面填要映射的主机端口
  -v /path/to/apache/default.conf:/etc/apache2/sites-available/000-default.conf \   #配置文件，默认配置请看下一部分
  -v /path/to/.htpasswd:/dav/.htpassword   #用户验证文件
  -v /path/to/dav-files:/dav/share     #前面是要共享的文件在主机的路径
  -v /path/to/letsencrypt/challenges:/acme/.well-known/acme-challenge \    #optional 可选配置
  --restart unless-stopped \
  xushier/webdav:latest
```
# 参数 Parameters
| 参数 Parameters | 功能 Function |
| :----: | :-----|
| `-p 80` | 网页界面 http WEBGUI |
| `-v /etc/apache2/sites-available/000-default.conf` | 配置文件 Config File |
| `-v /dav/.htpassword` | 用户验证文件 Auth File |
| `-v /dav/share` | 共享文件路径 Dav-files |
# 配置文件 000-default.conf    Config File
```
DavLockDB ${APACHE_LOCK_DIR}/DAVLock
<VirtualHost *:80>

        ServerAdmin webmaster@localhost
        DocumentRoot /dav/share
        ServerName localhost:80
        Alias /.well-known/acme-challenge/ /acme/.well-known/acme-challenge

        <Directory /dav/share>
            Options Indexes
            DAV On
            AuthType Basic
            AuthName "webdav"
            AuthUserFile /dav/.htpassword
            Require valid-user
        </Directory>

</VirtualHost>
```
# 多用户验证文件 Multi-User Auth Config
默认配置文件验证方式为`Basic`，如需修改为`Digest`，在配置文件里修改即可。用户及密码可在容器内使用`htpassword`命令生成，也可在`htpasswd`在线生成网站生成。

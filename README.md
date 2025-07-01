# jxwaf 4.2

# 依赖安装
```bash
sudo yum update -y
sudo yum install docker git
sudo service docker start
sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo usermod -a -G docker ec2-user
exit
docker info

git clone https://github.com/0671/jxwaf-docker-file
```

# 网络要求
网络链路：客户端-->CDN-->ALB-->JXWAF检测节点-->业务ALB-->业务节点:业务端口
```
流量接入：CDN/ALB ----> JXWAF检测节点:80/443
流量转发：检测节点 ----> 业务ALB/业务节点:业务端口
配置获取: 检测节点 ----> 管理节点:8000
日志上传: 检测节点 ----> 日志节点:8877
日志查询: 管理节点 ----> 日志节点:9000
```

# 管理节点
```bash
cd jxwaf-docker-file/prod_env/jxwaf-mini-server
docker-compose up -d
curl http://127.0.0.1:8000/
```
# 检测节点
```bash
cd jxwaf-docker-file/prod_env/jxwaf
vim docker-compose.yml
# 修改文件中的 JXWAF_SERVER 和 WAF_AUTH
    # JXWAF_SERVER 的值为 jxwaf 控制台服务器地址，这里为 http://10.206.0.10:8000 ，注意这里地址不能带路径
    # WAF_AUTH 为 控制台 系统配置 -> 基础信息 中 waf_auth 的值
docker-compose up -d
# 在 控制台中 运营中心 -> 节点状态 查看节点是否上线
```
# 日志节点
```bash
cd jxwaf-docker-file/prod_env/jxlog
docker-compose up -d
# 部署完成后，在 控制台中 系统配置 -> 日志传输配置 完成如下配置
    # 日志服务器地址：日志节点ip
    # 日志服务器端口：8877
# 在 控制台 系统配置 -> 日志查询配置 完成如下配置
    # ClickHouse服务器地址：日志节点ip
    # ClickHouse服务器端口：9000
    # 用户名：jxlog
    # 密码：jxlog
    # 数据库名称：jxwaf
```

# 效果验证
在控制台 防护管理 -> 网站配置 ，点击新建网站，参考如下配置进行设置：
```
域名/ip：检测节点ip
协议类型：http
源站地址：www.jxwaf.com
源站端口：443
回源协议：https
```
在检测节点下 执行验证命令：
```bash
cd jxwaf-docker-file/waf_test
python waf_poc_test.py -u http://检测节点ip
```
运行 waf 测试脚本后，即可在 控制台 运营中心 -> 攻击事件 查看防护效果

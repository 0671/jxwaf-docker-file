# jxwaf 4.2

```bash
sudo yum update -y
sudo yum install docker git
sudo service docker start
sudo usermod -a -G docker ec2-user
docker info

git clone https://github.com/0671/jxwaf-docker-file
```

# 管理节点
```bash
cd jxwaf-docker-file/prod_env/jxwaf-mini-server
docker compose  up -d
```
# 检测节点
```bash
cd jxwaf-docker-file/prod_env/jxwaf
vim docker-compose.yml
# 修改文件中的 JXWAF_SERVER 和 WAF_AUTH
    # JXWAF_SERVER 的值为 jxwaf 控制台服务器地址，这里为 http://10.206.0.10:8000 ，注意这里地址不能带路径
    # WAF_AUTH 为 系统配置 -> 基础信息 中 waf_auth 的值
docker compose  up -d
```
# 日志节点
```bash
cd jxwaf-docker-file/prod_env/jxlog
docker compose  up -d
# 部署完成后，在控制台中 系统配置 -> 日志传输配置
```

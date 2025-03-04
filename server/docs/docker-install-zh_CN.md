[English](docker-install.md) | 简体中文

##  说明
Docker版只为快速体验使用，请不要在生产环境下使用!
该版本中只包括了依赖组件和Elkeid Server的部署, Agent的部署请参考[Agent安装步骤](../../agent/README-zh_CN.md)。

##  依赖
docker-ce >= 18  
docker-compose >= 1.20  
Golang >=1.16(建议)
> Golang安装请参照官方文档：https://golang.org/doc/install  
> Docker安装请参考官方文档：https://docs.docker.com/engine/install/  
> Docker-Compose安装请参考官方文档: https://docs.docker.com/compose/install/

##  安装步骤
Step1. 下载源码
```
git clone https://github.com/bytedance/Elkeid.git
```
Step2. 启动依赖组件Kafka/Mongodb/Redis
```
cd Elkeid/server/build
docker-compose up
#如果服务无异常，可以放后台运行
# docker-compose up -d
```
服务起来后，`docker ps`会看到4个docker服务elkeid_kafka、elkeid_mongodb、elkeid_redis、elkeid_zookeeper  
> 如果提示获取 docker image 失败，请检查网络并重试，或者给 docker pull 挂上 http/https 代理

Step3. 编译，并将压缩包分别拷贝到对应的部署目录下
```
cd Elkeid/server/build && ./build.sh 

# 将生成3个压缩包
#service_discovery-*.tar.gz
#manager-*.tar.gz
#agent_center-*.tar.gz
```
Step4. 启动ServiceDiscovery
```
tar xvf service_discovery-*.tar.gz
cd service_discovery && ./sd
```
Step5. 启动Manager
```
tar xvf manager-*.tar.gz
cd manager 

#新增用户
./init -c conf/svr.yml -t addUser -u hids_test -p hids_test

#index新增Mongodb索引
./init -c conf/svr.yml -t addIndex -f conf/index.json

./manager
```
Step6. 启动AgentCenter
```
tar xvf agent_center-*.tar.gz
cd agent_center  && ./agent_center
```
## 开始使用
安装完成后，可跑测试脚本简单验证连通性
```
cd server/agent_center/test && go run grpc_client.go
```
将ServerDiscovery的地址配置到[Agent](../../agent/README-zh_CN.md)中，编译并部署Agent，即可通过[Manager API](../README-zh_CN.md)查看Agent在线情况。

可以从KAFKA中消费Agent数据进行后续处理。
```
cd server/agent_center/test && go run kafka_comsumer.go
```

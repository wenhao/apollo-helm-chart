### Create k8s Apollo namespace

```bash
kubectl create namespace apollo
```

### Install MySQL

```bash
helm upgrade -i apollo \
    -f ./mysql/values.yaml \
    -n apollo \
    stable/mysql
```

Uninstall if needs

```bash
helm uninstall -n apollo apollo
```

### Install Apollo Service

```bash
helm upgrade -i apollo-service-dev \
    --set configdb.host=apollo-mysql.apollo \
    --set configdb.dbName=ApolloConfigDB \
    --set configdb.userName=root \
    --set configdb.password=password \
    --set configService.replicaCount=1 \
    --set adminService.replicaCount=1 \
    -n apollo \
    apollo/apollo-service
```

Uninstall if needs

```bash
helm uninstall -n apollo apollo-service-dev
```

### Install Apollo Portal

```bash
helm upgrade -i apollo-portal \
    -f ./portal-values.yaml \
    --set portaldb.host=apollo-mysql.apollo \
    --set configdb.dbName=ApolloPortalDB \
    --set portaldb.userName=root \
    --set portaldb.password=password \
    --set config.envs="dev" \
    --set config.metaServers.dev=http://apollo-service-dev-apollo-configservice:8080 \
    --set replicaCount=1 \
    --set ingress.enabled=true \
    -n apollo \
    apollo/apollo-portal
```

Deploy with multiple metaServers

**配置项统一存储在ApolloPortalDB.ServerConfig表中，也可以通过管理员工具 - 系统参数页面进行配置（apollo.portal.envs），无特殊说明则修改完一分钟实时生效。**

```bash
helm upgrade -i apollo-portal \
    -f ./portal-values.yaml \
    --set portaldb.host=apollo-mysql.apollo \
    --set configdb.dbName=ApolloPortalDB \
    --set portaldb.userName=root \
    --set portaldb.password=password \
    --set config.envs="dev\,st\,uat" \
    --set config.metaServers.dev=http://apollo-service-dev-apollo-configservice:8080 \
    --set config.metaServers.st=http://apollo-service-st-apollo-configservice:8080 \
    --set config.metaServers.uat=http://apollo-service-uat-apollo-configservice:8080 \
    --set replicaCount=1 \
    --set ingress.enabled=true \
    -n apollo \
    apollo/apollo-portal
```

Uninstall if needs

```bash
helm uninstall -n apollo apollo-portal
```

### Access Ingress

Edit /etc/hosts add follow lies:

```text
sudo echo "$(minikube ip) apollo.devops.com" | sudo tee -a /etc/hosts
```
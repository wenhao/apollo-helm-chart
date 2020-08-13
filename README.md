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
helm install apollo-portal \
    --set portaldb.host=apollo-mysql.apollo \
    --set configdb.dbName=ApolloPortalDB \
    --set portaldb.userName=root \
    --set portaldb.password=password \
    --set config.envs="dev" \
    --set config.metaServers.dev=http://apollo-service-dev-apollo-configservice:8080 \
    --set replicaCount=1 \
    --set ingress.enabled=true \
    --set ingress.hosts.host=apollo.devops.com
    --set ingress.hosts.paths=/ \
    -n apollo \
    apollo/apollo-portal
```

Uninstall if needs

```bash
helm uninstall -n apollo apollo-portal
```
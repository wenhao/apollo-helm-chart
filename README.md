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

Uninstall if needs

```bash
helm uninstall -n apollo apollo-portal
```

### Access Ingress

Edit /etc/hosts add follow lies:

```text
sudo echo "$(minikube ip) apollo.devops.com" | sudo tee -a /etc/hosts
```
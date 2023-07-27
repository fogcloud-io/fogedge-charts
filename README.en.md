# fogedge-charts
[![standard-readme compliant](https://img.shields.io/badge/licence-Apache%202.0-blue)](https://www.apache.org/licenses/LICENSE-2.0) [![standard-readme compliant](https://img.shields.io/static/v1?label=official&message=demo&color=<COLOR>)](https://app.fogcloud.io)

[FogEdge]() is a edge gateway software.

## Prerequisites

- [install kubernetes](https://docs.k3s.io/installation) 1.19+ 
- [install and set up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/) 1.19+
- [install helm](https://helm.sh/docs/intro/install/) 3+

## Get repo

```console
helm repo add fogcloud-charts https://fogcloud-io.github.io/fogedge-charts
helm repo update
helm pull fogedge-charts/fogedge --untar
```

## Install Chart

1. cp fogedge-charts/values.yaml ./myvalues.yaml
2. edit myvalues.yaml
3. install fogedge-charts
```console
export NAMESPACE_NAME=fogedge
export RELEASE_NAME=fogedge
kubectl create namespace ${NAMESPACE_NAME}
helm install -f myvalues.yaml ${RELEASE_NAME} -n ${NAMESPACE_NAME} ./fogedge
```
5. upgrade fogedge-charts
```console
helm upgrade -f myvalues.yaml ${RELEASE_NAME} -n ${NAMESPACE_NAME} ./fogedge 
```

## Uninstall Chart

```console
helm uninstall ${RELEASE_NAME} -n ${NAMESPACE_NAME}
```

## Configuration

| Parameter | Description | Default |
| --- | --- | --- |
| `imagePullPolicy` | 镜像拉取策略 | `Always` | 
| **expose** | | | 
| `expose.type` | 如何暴露服务：`Ingress`、`ClusterIP`、`NodePort`或`LoadBalancer`，其他值将被忽略，服务的创建将被跳过。| `ClusterIP` |
| `expose.insecureOSS` | 不安全的oss下载 | `true` | 
| `expose.hosts.api` | api服务域名，用于前端服务访问后端api | `localhost` | 
| `expose.hosts.mqtt` | mqtt服务域名，用于前端服务访问mqtt-websocket服务 | `localhost` | 
| `expose.tls.enabled` | 是否启用HTTP接口 tls | `false` |
| `expose.tls.cert.certSource` | api服务证书的来源：`auto`或`manual`；1）`auto`：生成自签名证书；2）`manual`：手动设置证书 | `auto` |
| `expose.tls.cert.secretName` | api服务所用证书对应的k8s secret资源名 | `fogedge-tls` |
| `expose.tls.cert.dnsName` | 当`expose.tls.cert.certSource`=`auto`时，用于生成x509证书 | `["fogcore", "mqtt-broker", "localhost"]` |
| `expose.tls.cert.ipTables` | 当`expose.tls.cert.certSource`=`auto`时，用于生成x509证书 | `["127.0.0.1"]` |
| `expose.tls.cert.ca` | 当`expose.tls.cert.certSource`=`manual`时可用，用于设置ca证书 | |
| `expose.Ingress.className` | ingress class资源名| `traefik` |
| `expose.Ingress.controller` | ingress controller类型 | `traefik.io/ingress-controller` |
| `expose.Ingress.annotations` | ingress注释，可以用来设置ingress部分参数 | `{}` |
| `expose.Ingress.hosts.webAdmin` | web服务域名，用于ingress路由 | `localhost` |
| `expose.Ingress.hosts.api` | api服务域名，用于ingress路由 | `localhost` |
| `expose.NodePort` |  |  |
| `expose.NodePort.externalTrafficPolicy` | 流量策略：`Cluster`或`Local`；1）`Cluster`：流量可以转发到其他k8s节点的pod，2）`Local`：流量只转发给本机的pod| `Local` | 
| `expose.NodePort.ports.webAdmin.httpPort` | web服务的NodePort端口，可用于外网暴露web服务 | `8888` |
| `expose.NodePort.ports.api.httpPort` | api服务的NodePort端口，可用于外网暴露api服务 | `8000` |
| `expose.LoadBalancer` | | |
| `expose.LoadBalancer.externalTrafficPolicy` | 流量策略：`Cluster`或`Local`；1）`Cluster`：流量可以转发到其他k8s节点的pod，2）`Local`：流量只转发给本机的pod | `Local` |
| `expose.LoadBalancer.ports.webAdmin.healthCheckNodePort` | 健康检查端口，用于外部slb检测web服务是否正常运行 | `8880` |
| `expose.LoadBalancer.ports.api.healthCheckNodePort` | 健康检查端口，用于外部slb检测api服务是否正常运行 | `8880` |
| **imagePullSecrets** | | |
| `imagePullSecrets` | 配置私有镜像仓库源 | |
| `imagePullSecrets[].registry` | 私有镜像仓库地址 | `""` | 
| `imagePullSecrets[].name` | k8s dockerconfigjson secret名称 | `""` | 
| `imagePullSecrets[].username` | 私有镜像仓库用户名 | `""` |
| `imagePullSecrets[].password`| 私有镜像仓库密码 | `""` |
| `storageClassName` |  | `local-path` |
| **fogcore** | api服务相关配置 | |
| `fogcore.restartPolicy` | pod重启策略：`Always` | `Always` |
| `fogcore.image` | 镜像地址 | `ghcr.io/fogcloud-io/fogcloud` | 
| `fogcore.replicas` | deployment复制节点数量 | `1` |
| `fogcore.strategy.type` | 应用更新策略：`RollingUpdate`，`Recreate`；1）`RollingUpdate`滚动更新；2）`Recreate`重启更新 | `RollingUpdate` |
| `fogcore.strategy.rollingUpdate.maxSurge` | 应用更新时最大新版本pod新增数量比例| `25%` |
| `fogcore.strategy.rollingUpdate.maxUnavailable` | 应用更新时的最大不可用pod数量 | `25%` |
| **mqttBroker** | | |
| `mqttBroker.type` | mqtt-broker类型：`nanomq`| `nanomq` |
| `mqttBroker.image` | mqtt-broker镜像 | `emqx/nanomq:0.18.2-slim` |
| `mqttBroker.persistence` | | |
| `mqttBroker.persistence.pvcExisted` | 是否使用已存在的pvc | `false`|
| `mqttBroker.persistence.pvc` | pvc名称 | `emqx-pvc` |
| `mqttBroker.persistence.storageClassName` | pvc绑定的`stogrageClassName` | `local-path` |
| `mqttBroker.nodeSelector.enabled` | 是否启用pod节点选择 | `false` |
| `mqttBroker.nodeSelector.key` | k8s节点名 | |   
| `mqttBroker.tls.enabled` | mqtt应用是否启用tls  | |
| `mqttBroker.tls.createWithCertFile` | 是否使用证书文件创建mqtt应用的sercret对象，启用mqttBroker.internal.tls时有效；若为true，可将*.crt（证书）, *.key（密钥）文件放到fogcloud-charts/configs/cert/mqtt目录下 |  | 

## Licence

[Apache 2.0 License](https://github.com/fission/.github/blob/main/LICENSE).

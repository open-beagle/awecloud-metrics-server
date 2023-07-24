# metrics-server

<https://github.com/kubernetes-sigs/metrics-server>

## git

```bash
git remote add upstream git@github.com:kubernetes-sigs/metrics-server.git
git fetch upstream
git merge v0.6.3
```

## build

```bash
# cache
docker run -it --rm \
-w /go/src/sigs.k8s.io/metrics-server/ \
-v $PWD/:/go/src/sigs.k8s.io/metrics-server/ \
registry.cn-qingdao.aliyuncs.com/wod/golang:1.20-alpine \
rm -rf vendor && go mod vendor

# golang
docker run -it --rm \
-w /go/src/sigs.k8s.io/metrics-server/ \
-v $PWD/:/go/src/sigs.k8s.io/metrics-server/ \
-e CI_WORKSPACE=/go/src/sigs.k8s.io/metrics-server \
-e PLUGIN_BINARY=metrics-server \
-e PLUGIN_MAIN=cmd/metrics-server \
registry.cn-qingdao.aliyuncs.com/wod/devops-go-arch:1.20-alpine
```

## cache

```bash
# 构建缓存-->推送缓存至服务器
docker run --rm \
  -e PLUGIN_REBUILD=true \
  -e PLUGIN_ENDPOINT=$PLUGIN_ENDPOINT \
  -e PLUGIN_ACCESS_KEY=$PLUGIN_ACCESS_KEY \
  -e PLUGIN_SECRET_KEY=$PLUGIN_SECRET_KEY \
  -e DRONE_REPO_OWNER="open-beagle" \
  -e DRONE_REPO_NAME="awecloud-metrics-server" \
  -e PLUGIN_MOUNT="./.git,./vendor" \
  -v $(pwd):$(pwd) \
  -w $(pwd) \
  registry.cn-qingdao.aliyuncs.com/wod/devops-s3-cache:1.0

# 读取缓存-->将缓存从服务器拉取到本地
docker run --rm \
  -e PLUGIN_RESTORE=true \
  -e PLUGIN_ENDPOINT=$PLUGIN_ENDPOINT \
  -e PLUGIN_ACCESS_KEY=$PLUGIN_ACCESS_KEY \
  -e PLUGIN_SECRET_KEY=$PLUGIN_SECRET_KEY \
  -e DRONE_REPO_OWNER="open-beagle" \
  -e DRONE_REPO_NAME="awecloud-metrics-server" \
  -v $(pwd):$(pwd) \
  -w $(pwd) \
  registry.cn-qingdao.aliyuncs.com/wod/devops-s3-cache:1.0
```

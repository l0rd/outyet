# Go example project

[![](https://img.shields.io/badge/Eclipse_Che-Hosted%20by%20Red%20Hat-525C86?logo=eclipse-che&labelColor=FDB940)](https://che-eclipse-che.apps.mloriedo-che-devex.devcluster.openshift.com/#https://github.com/l0rd/outyet)

This repository contains a Go program that demonstrates the language, standard libraries, and tools.

The example has been copied from [golang/example](https://github.com/golang/example/)

A web server that answers the question: "Is Go 1.x out yet?"

Topics covered:

* Command-line flags ([flag](//golang.org/pkg/flag/))
* Web servers ([net/http](//golang.org/pkg/net/http/))
* HTML Templates ([html/template](//golang.org/pkg/html/template/))
* Logging ([log](//golang.org/pkg/log/))
* Long-running background processes
* Synchronizing data access between goroutines ([sync](//golang.org/pkg/sync/))
* Exporting server state for monitoring ([expvar](//golang.org/pkg/expvar/))
* Unit and integration tests ([testing](//golang.org/pkg/testing/))
* Dependency injection
* Time ([time](//golang.org/pkg/time/))

### Build the container image

<!---

If using ko, debugging using `attach local` config doesn't work because the source code is not included in the container.
Debugging using `attach remote` works with the image build with ko if we use the option `--debug` but then the application is not started (dlv is started).
https://ko.build/reference/ko_build/
I haven't found a way to address this problem so I am using a podman build to create the image.

```bash
echo $CR_PAT | ko login ghcr.io -u <gh-username> --password-stdin
export KO_DOCKER_REPO=ghcr.io/l0rd/outyet

ko build --bare --disable-optimizations  \
      --image-label "org.opencontainers.image.source=https://github.com/l0rd/outyet" \
      --image-label "org.opencontainers.image.description=A very simple go app" \
      --image-label "org.opencontainers.image.licenses=Apache-2.0" \
      --image-label "org.opencontainers.image.description=A sample Go web app" .
```
-->

```bash
podman build -t ghcr.io/l0rd/outyet:latest --arch amd64 \
      --label "org.opencontainers.image.source=https://github.com/l0rd/outyet" \
      --label "org.opencontainers.image.description=A very simple go app" \
      --label "org.opencontainers.image.licenses=Apache-2.0" \
      --label "org.opencontainers.image.description=A sample Go web app" .
podman push ghcr.io/l0rd/outyet:latest
```

### Run the application on Kubernetes

```bash
kubectl apply -f config/
kubectl expose deployment outyet --type=NodePort --port=8080
```

### Delete the application

```bash
ko delete -f config/
```

### Build the container to debug the application

```bash
podman build -t ghcr.io/l0rd/outyet-dev:latest --arch amd64 \
      --label "org.opencontainers.image.source=https://github.com/l0rd/outyet" \
      --label "org.opencontainers.image.description=A very simple go app" \
      --label "org.opencontainers.image.licenses=Apache-2.0" -f Dockerfile.dev .
podman push ghcr.io/l0rd/outyet-dev:latest
```
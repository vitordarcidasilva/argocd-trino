# DataPains Trino Kubernetes

[Medium Article For Reference]()

# Pre Requisites

# Setup

## MinIO With Metastore

In this workshop I provide to you a setup for a MinIO with a Metastore (utilising postgres internally)
to successfully query delta tables via trino.

### Docker

If you are using M1 chip:

```bash
make build-image COMPONENT=metastore DOCKER_BUILD="buildx build --platform linux/amd64" CONTEXT="tools/docker"
```

Otherwise

```bash
make build-image COMPONENT=metastore CONTEXT="tools/docker"
```

### Metastore - Vanilla - Local

```bash
make deploy-local-metastore ENV=local
```

To delete

```bash
make delete-local-metastore ENV=local
```

#### Statefulset

For `Hive`, `Postgres`, `MinIO` we choose to use `StatefulSet` as the deployment type over `Deployment`.

A StatefulSet should be chosen for stateful components that require predictable Pod identities,
ordered rollouts, and stable storage access.

Deployment should be chosen instead when your service is stateless,
doesn't require persistent storage, and won't be affected by changes to Pod identities.

## Trino

### Helm

First add the trino helm repo!

```bash
helm repo add trino https://trinodb.github.io/charts
```

#### values.yaml

We use default values for the deployment, but please note section:

```yaml
additionalCatalogs:
  lakehouse: |-
    connector.name=delta
    hive.metastore.uri=thrift://hive-service.metastore:9083
```

This will ensure we can query our tables from our metastore.

```bash
make deploy-local-trino NAMESPACE=trino
```

And to delete

```bash
make delete-local-trino
```

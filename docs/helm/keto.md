# ORY Keto Helm Chart

The ORY Keto Helm Chart helps you deploy ORY Keto on Kubernetes using Helm.

## Installation

To install ORY Keto, the following values must be set
([documentation](https://github.com/ory/keto/blob/master/docs/config.yaml)):

* `keto.config.dsn`

If you wish to install ORY Keto with an in-memory database run:

```bash
$ helm install \
    --set 'keto.config.dsn=memory' \
    ory/keto
```

### With SQL Database

To run ORY Keto against a SQL database, set the connection string. For example:

```bash
$ helm install \
    ...
    --set 'dsn=postgres://foo:bar@baz:1234/db' \
    ory/keto
```

This chart does not require MySQL, PostgreSQL, or CockroachDB as dependencies because we strongly encourage
you not to run a database in Kubernetes but instead recommend to rely on a managed SQL database such as Google
Cloud SQL or AWS Aurora.

### With Google Cloud SQL

To connect to Google Cloud SQL, you could use
the [`gcloud-sqlproxy`](https://github.com/rimusz/charts/tree/master/stable/gcloud-sqlproxy) chart:

```bash
$ helm upgrade pg-sqlproxy rimusz/gcloud-sqlproxy --namespace sqlproxy \
    --set 'serviceAccountKey="$(cat service-account.json | base64 | tr -d '\n')"' \
    ...
```

When bringing up ORY Keto, set the host to `pg-sqlproxy-gcloud-sqlproxy` as documented
[here](https://github.com/rimusz/charts/tree/master/stable/gcloud-sqlproxy#installing-the-chart):

```bash
$ helm install \
    ...
    --set 'dsn=postgres://foo:bar@pg-sqlproxy-gcloud-sqlproxy:5432/db' \
    ory/keto
```

## Configuration

You can pass your [ORY Keto configuration file](https://github.com/ory/keto/blob/master/docs/config.yaml)
by creating a yaml file with key `keto.config`

```yaml
# keto-config.yaml

keto:
  config:
    # e.g.:
    serve:
      port: 8080
   # ...
```

and passing that as a value override to helm:

```bash
$ helm install -f ./path/to/keto-config.yaml ory/keto
```

Additionally, the following extra settings are available:

- `autoMigrate` (bool): If enabled, an `initContainer` running `keto migrate sql` will be created.

### Keto Maester
This chart includes a helper chart in the form of [Keto Maester](https://github.com/ory/k8s/blob/master/docs/helm/keto-maester.md), a Kubernetes controller, which manages OAuth2 clients using the `oauth2clients.keto.ory.sh` custom resource. By default, this component is enabled and installed together with Keto. However, it can be disabled by setting the proper flag:

```bash
$ helm install \
    --set 'maester.enabled=false' \
    ory/keto
```

# Development

Deploy Cloud Run site using Terraform

## Prerequisites

- Install [Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli)
- Install [`ko`](https://github.com/google/ko)
- Set the `KO_DOCKER_REPO` env var to the GCR repository you'd like to deploy to (e.g.,
`KO_DOCKER_REPO=gcr.io/gcping-devrel`)
- Install the current version of [Node.js](https://nodejs.org/en/) (>= v16).
- Ensure your credentials are fresh with:
```
$ gcloud auth login                      # Used by ko
$ gcloud auth application-default login  # Used by Terraform
```
- Run `terraform init` to fetch the Terraform state, etc.
- In Cloud Shell, if you see errors about IPv6 addresses not resolving, run
  `./bin/prefer-ipv4.sh`

For an environment in cloud run shell:

- A bucket with name `gcping-tf-state-gcpingmadrid` is created.
- Terraform, cloud cli and node are already installed.
- Install [`ko`](https://github.com/google/ko)

```
VERSION=0.11.2 # choose the latest version
OS=Linux     # or Darwin
ARCH=x86_64  # or arm64, i386, s390x
curl -L https://github.com/google/ko/releases/download/v${VERSION}/ko_${VERSION}_${OS}_${ARCH}.tar.gz | tar xzf - ko
chmod +x ./ko
```

- Set the `KO_DOCKER_REPO` env var to the GCR repository you'd like to deploy to (e.g.,
  `KO_DOCKER_REPO=gcr.io/gcping-349912`)

- Creadentials are ensured by cloud run shell

- Run `terraform init` to fetch the Terraform state, etc.
- In Cloud Shell, if you see errors about IPv6 addresses not resolving, run
  `./bin/prefer-ipv4.sh`
- We need to enable cloud run admin api

## Build the frontend

```
$ cd web
$ npm install
$ npm run build  # generate the frontend
```

## Deploy

```
$ export KO_DOCKER_REPO=gcr.io/gcping-349912 # prod only, use other repo for dev
$ terraform apply -var image=$(../ko publish -P ./cmd/ping/)
```

This deploys the ping service to all Cloud Run regions and configures a global
HTTPS Load Balancer with Google-managed SSL certificate for
`global.gcping.com`.

## Run frontend locally

First, start the Ping server with:

``` shell
# starts a server on localhost:8080
go run ./cmd/ping/main.go
```

Next, within the `web` directory, run:

``` shell
# starts a frontend server at localhost:1234
npm start
```

The frontend server when run locally is configured to proxy all API requests to
`localhost:8080`.


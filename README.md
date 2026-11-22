# Docker-socket-proxy - Expose Docker Engine Socket via HTTPS with Authentication

This solution allows to communicate with Docker Engine API remotely, over secure HTTPS, with authentication;  
while Docker daemon runs with default configuration.

## Features

- Exposes Docker Engine socket via https
- Exposes required endpoints only:
  - /containers/json - get a list of containers (running, created, all)
- Uses HTTPS protocol for secure communication
- Uses HTTP AUTH BASIC for authentication

## Why

While Docker Engine can expose its API over http, there are few limitations:
- It exposes all API endpoints at one. [Remote Access](https://docs.docker.com/engine/daemon/remote-access/)
- Restart is required to apply a change for the `hosts` option, which interrupts running containers
- Docker can use TLS Client Certificate for authentication. That can be found a bit complicated. [Protect Access](https://docs.docker.com/engine/security/protect-access/)

Therefore the own solution has been implemented, which allows to enable and disable remote access any time,
without services interruption, and being secure.

## Prerequisites

### TLS Certificates

We need to generate Certificate Authority which will be used for two activities:
- to issue a server TLS certificate
- on a client side to validate a peer

In the example below we use `easyrsa` script delivered by the [easy-rsa](https://wiki.archlinux.org/title/Easy-RSA) package.  
This package is widely available across many Linux distributions.

Initialize a workspace. Choose a secure place, navigate there and run the command
```
easyrsa init-pki
```

Generate CA:
```
easyrsa build-ca
```

Issue a server certificate. "localhost" is used as example. Choose appropriate, the one to be used in requests.
```
easyrsa build-server-full --no-pass localhost
```

### Username and Password for Authentication

We need to create a file, to be used by nginx for [http basic authentication](https://nginx.org/en/docs/http/ngx_http_auth_basic_module.html).  
The file contains username and password pairs per line.  
Username and password are delimited by colon.  
Password can be generated using the `htpasswd` utility from the Apache HTTP Server distribution or the `openssl passwd` command.

## Usage

Investigate `compose.yml` for the right locations of TLS certificates and the password file. Update if needed.

Start the service:
```
docker compose up -d
```

## Tests

Get list of all containers:
```
curl -v --cacert ./pki/ca.crt https://username1:password1@localhost:2475/containers/json?all=true
```

## Donate

[Click here to support me](https://github.com/sponsors/abriel)

## License

Copyright (c) 2026-2026 Dmitry Pisarev. MIT Licensed, see [LICENSE](LICENSE.md) for details.

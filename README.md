# Pi-hole over HTTPS Helm chart

## Proof of Concept

This Helm chart deploy [Pi-hole](https://pi-hole.net/) with a [DNS over HTTPS proxy](https://github.com/facebookexperimental/doh-proxy).  
DNS over TLS gateway is not functional yet.

## Requirements
- [cert-manager](https://github.com/jetstack/cert-manager)
- [LetsEncrypt Cluster Issuer](https://cert-manager.readthedocs.io/en/latest/getting-started/3-configuring-first-issuer.html) named "letsencrypt-production" (the name can be changed in `values.yaml` file)
- [ingress-nginx](https://github.com/kubernetes/ingress-nginx)

## Installation

### Add repository

Follow instructions at [https://adrienkuhn.github.io/helm-repo/](https://adrienkuhn.github.io/helm-repo/)

### Install

```bash
helm install poh adrienkuhn-helm-repo/poh
```

## Configuration

| Variable                          | Description                                      | Default                |
| --------------------------------- | ------------------------------------------------ | ---------------------- |
| `nameOverride`                    | Chart name override                              | `""`                   |
| `fullnameOverride`                | Full Chart name override                         | `""`                   |
| `pihole.name`                     | Name for the Pi-hole deployment                  | `pihole`               |
| `pihole.replicaCount`             | Number of replicas                               | `1`                    |
| `pihole.restartPolicy`            | Restart policy                                   | `Always`               |
| `pihole.image.repository`         | Docker repository                                | `pihole/pihole`        |
| `pihole.image.tag`                | Docker tag                                       | `v5.1.2`               |
| `pihole.image.pullPolicy`         | Pull policy                                      | `Always`               |
| `pihole.existingSecret`           | Name of an existing secret                       | `nil`                  |
| `pihole.secrets.webPassword`      | Web admin password, if existingSecret is not set | `changeMe`             |
| `pihole.config.virtualHost`       | Pi-hole virtual host                             | `127.0.0.1`            |
| `pihole.config.tz`                | Pi-hole timezone                                 | `UTC`                  |
| `pihole.config.dns1`              | Upstream DNS server 1                            | `9.9.9.9`              |
| `pihole.config.dns2`              | Upstream DNS server 2                            | `149.112.112.112`      |
| `pihole.config.dnsSec`            | Enable DNSSEC                                    | `1`                    |
| `pihole.resources`                | Resources requests and limits                    | `{}`                   |
| `pihole.service.type`             | Service type                                     | `ClusterIP`            |
| `pihole.service.http.port`        | HTTP service port                                | `80`                   |
| `pihole.service.http.targetPort`  | HTTP container port                              | `80`                   |
| `pihole.service.http.protocol`    | HTTP service protocol                            | `TCP`                  |
| `pihole.service.dns.port`         | DNS service port                                 | `53`                   |
| `pihole.service.dns.targetPort`   | DNS container port                               | `53`                   |
| `pihole.service.dns.protocol`     | DNS service protocol                             | `TCP`                  |
| `pihole.nodeSelector`             | Node selectors                                   | `{}`                   |
| `pihole.tolerations`              | Node tolerations                                 | `[]`                   |
| `pihole.affinity`                 | Node affinities                                  | `{}`                   |
| `dohProxy.name`                   | Name for the DoH proxy deployment                | `doh-http-proxy`       |
| `dohProxy.enabled`                | Enable DoH proxy                                 | `false`                |
| `dohProxy.replicaCount`           | Number of replicas                               | `1`                    |
| `dohProxy.restartPolicy`          | Restart Policy                                   | `Always`               |
| `dohProxy.image.repository`       | Docker repository                                | `krewh/doh-httpproxy`  |
| `dohProxy.image.tag`              | Docker tag                                       | `0.0.3`                |
| `dohProxy.image.pullPolicy`       | Pull policy                                      | `Always`               |
| `dohProxy.resources`              | Resources requests and limits                    | `{}`                   |
| `dohProxy.service.type`           | Service type                                     | `ClusterIP`            |
| `dohProxy.service.port`           | HTTP service port                                | `8080`                 |
| `dohProxy.service.targetPort`     | HTTP container port                              | `8080`                 |
| `dohProxy.service.protocol`       | HTTP service protocol                            | `TCP`                  |
| `dohProxy.nodeSelector`           | Node selectors                                   | `{}`                   |
| `dohProxy.tolerations`            | Node tolerations                                 | `[]`                   |
| `dohProxy.affinity`               | Node affinities                                  | `{}`                   |
| `nginx.name`                      | Name for the nginx deployment                    | `nginx`                |
| `nginx.replicaCount`              | Number of replicas                               | `1`                    |
| `nginx.restartPolicy`             | Restart policy                                   | `Always`               |
| `nginx.image.repository`          | Docker repository                                | `krewh/hardened-nginx` |
| `nginx.image.tag`                 | Docker tag                                       | `latest`               |
| `nginx.image.pullPolicy`          | Pull policy                                      | `Always`               |
| `nginx.adminAuthorizedIps`        | IP range authorized to access the Pi-hole web UI | `0.0.0.0/0`            |
| `nginx.dnsOverTlsGateway.enabled` | Enable the DoT gateway                           | `false`                |
| `nginx.dnsQueryLogging.enabled`   | Enable DNS query logging in Nginx logs           | `false`                |
| `nginx.service.type`              | Service type                                     | `ClusterIP`            |
| `nginx.service.https.port`        | HTTP service port                                | `443`                  |
| `nginx.service.https.targetPort`  | HTTP container port                              | `443`                  |
| `nginx.service.https.protocol`    | HTTP service protocol                            | `TCP`                  |
| `nginx.service.dot.port`          | DoT Gateway service port                         | `853`                  |
| `nginx.service.dot.targetPort`    | DoT Gateway container port                       | `853`                  |
| `nginx.service.dot.protocol`      | DoT Gateway service protocol                     | `TCP`                  |
| `nginx.resources`                 | Resources requests and limits                    | `{}`                   |
| `nginx.nodeSelector`              | Node selectors                                   | `{}`                   |
| `nginx.tolerations`               | Node tolerations                                 | `[]`                   |
| `nginx.affinity`                  | Node affinities                                  | `{}`                   |
| `ingress.enabled`                 | Enable ingress                                   | `false`                |
| `ingress.annotations`             | Ingress annotations                              | See values.yaml        |
| `ingress.hosts`                   | Ingress hosts configuration                      | See values.yaml        |
| `ingress.tls`                     | Ingress TLS configuration                        | `[]`                   |


## DNS Queries

You should now be able to do DNS queries with any DoH clients.

### Linux / MacOS
- [cloudflared-proxy](https://developers.cloudflare.com/1.1.1.1/dns-over-https/cloudflared-proxy/)
- [dnscrypt-proxy 2](https://github.com/jedisct1/dnscrypt-proxy)

### Android
- [Intra](https://play.google.com/store/apps/details?id=app.intra)

## Contributing

This is a Proof of Concept and contributions are welcome.

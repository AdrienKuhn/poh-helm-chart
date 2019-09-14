# Pi-hole over HTTPS Helm chart

## Proof of Concept

This Helm chart deploy [Pi-hole](https://pi-hole.net/) with a [DNS over HTTPS proxy](https://github.com/facebookexperimental/doh-proxy) on a single pod in a Kubernetes cluster.

## Requirements
- [cert-manager](https://github.com/jetstack/cert-manager)
- [LetsEncrypt Cluster Issuer](https://cert-manager.readthedocs.io/en/latest/getting-started/3-configuring-first-issuer.html) named "letsencrypt-production" (the name can be changed in `values.yaml` file)
- [ingress-nginx](https://github.com/kubernetes/ingress-nginx)

## Installation

### Add repository

Follow instructions at [https://adrienkuhn.github.io/helm-repo/](https://adrienkuhn.github.io/helm-repo/)

### Install

```bash
helm install adrienkuhn-helm-repo/poh \
    --name=poh \
    --namespace=poh \
    --version 0.0.1 \
    --set config.ServerIP={Load Balancer IP} \
    --set config.VIRTUAL_HOST={pihole.example.org} \
    --set secrets.WEBPASSWORD={Pihole UI strong password} \
    --set ingress.enabled=true \
    --set ingress.http.hosts\[0\]={pihole.example.org} \
    --set ingress.http.tls\[0\].hosts\[0\]={pihole.example.org} \
    --set ingress.http.tls\[0\].secretName=pihole-tls \
    --set ingress.doh.hosts\[0\]={dns.example.org} \
    --set ingress.doh.tls\[0\].hosts\[0\]={dns.example.org} \
    --set ingress.doh.tls\[0\].secretName=doh-tls
```

## DNS Queries

You should now be able to do DNS queries with any DoH clients.

### Linux / MacOS
- [cloudflared-proxy](https://developers.cloudflare.com/1.1.1.1/dns-over-https/cloudflared-proxy/)
- [dnscrypt-proxy 2](https://github.com/jedisct1/dnscrypt-proxy)

### Android
- [Intra](https://play.google.com/store/apps/details?id=app.intra)

## Contributing

This is a Proof of Concept and contributions are welcome.

Use as an 'extraContainer' on the Vault Helm chart to notify Vault to reload its TLS certificate when it changes (because cert-manager renewed it, for example).

Add something like the following to your Helm chart values:

```yaml
  extraContainers:
    - name: cert-watcher
      image: ghcr.io/flyte/docker-vault-cert-reloader:1.0.4
      args:
        - /var/run/secrets/vault-tls/tls.crt
      volumeMounts:
        - name: vault-tls
          mountPath: /var/run/secrets/vault-tls
          readOnly: true
  shareProcessNamespace: true
```

To  update this image please use the following commands
```bash
aws ecr get-login-password --region eu-central-1 --profile infrastructure-services | docker login --username AWS --password-stdin 905418369299.dkr.ecr.eu-central-1.amazonaws.com
docker buildx build --platform linux/arm64 -t 905418369299.dkr.ecr.eu-central-1.amazonaws.com/internal-services/vault/vault-cert-reloader:latest --push .
```
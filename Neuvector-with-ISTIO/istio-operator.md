# Strict External Service Registry
Change the meshConfig.outboundTrafficPolicy.mode option to REGISTRY_ONLY
- This explicitly tells Envoy Proxy to deny any outbound requests unless it's registered with "service entry"
- This is for advanced configuration and could break your applications. Test in a dev environment first!

```yaml
spec:
  meshConfig:
    outboundTrafficPolicy:
      mode: REGISTRY_ONLY
```

# Set Minimum Mesh TLS Version
- Sets the minimum TLS version between Envoy proxy sidecars

```yaml
spec:
  meshConfig:
    meshMTLS:
      minProtocolVersion: TLSV1_3
```


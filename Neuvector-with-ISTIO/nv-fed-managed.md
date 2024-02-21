```yaml
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: public-gateway
  namespace: istio-system
spec:
  selector:
    app: istio-ingressgateway
  servers:
    - hosts:
        - '*'
      port:
        name: http
        number: 80
        protocol: HTTP
      tls:
        httpsRedirect: true
    - hosts:
        - neuvector-fed-managed.10-7-2-158.sslip.io
      port:
        name: neuvector-fed-managed
        number: 443
        protocol: HTTPS
      tls:
        maxProtocolVersion: TLSV1_3
        minProtocolVersion: TLSV1_2
        mode: PASSTHROUGH
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: neuvector-fedmanaged-vs
  namespace: cattle-neuvector-system
spec:
  gateways:
    - istio-system/public-gateway
  hosts:
    - neuvector-fed-managed.10-7-2-158.sslip.io
  tls:
    - match:
        - port: 443
          sniHosts:
            - neuvector-fed-managed.10-7-2-158.sslip.io
      route:
        - destination:
            host: >-
              neuvector-svc-controller-fed-managed.cattle-neuvector-system.svc.cluster.local
            port:
              number: 10443
          weight: 100
```
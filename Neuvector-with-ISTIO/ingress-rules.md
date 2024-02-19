# TLS Termination at edge with Neuvector UI

```yaml
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: public-ingress-gw
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
        - neuvector.10-7-2-67.sslip.io
      port:
        name: neuvector-ui
        number: 443
        protocol: HTTPS
      tls:
        credentialName: neuvector-tls-cert
        maxProtocolVersion: TLSV1_3
        minProtocolVersion: TLSV1_2
        mode: SIMPLE
    - hosts:
        - neuvector-fed-master.10-7-2-67.sslip.io
      port:
        name: neuvector-fed-master
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
  name: neuvector-fedmaster-vs
  namespace: cattle-neuvector-system
spec:
  gateways:
    - istio-system/public-ingress-gw
  hosts:
    - neuvector-fed-master.10-7-2-67.sslip.io
  tls:
    - match:
        - port: 443
          sniHosts:
            - neuvector-fed-master.10-7-2-67.sslip.io
      route:
        - destination:
            host: >-
              neuvector-svc-controller-fed-master.cattle-neuvector-system.svc.cluster.local
            port:
              number: 11443
          weight: 100
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: neuvector-vs
  namespace: cattle-neuvector-system
spec:
  gateways:
    - istio-system/public-ingress-gw
  hosts:
    - neuvector.10-7-2-67.sslip.io
  http:
    - match:
        - port: 443
        - uri:
            prefix: /
      name: neuvector-ui
      route:
        - destination:
            host: neuvector-service-webui.cattle-neuvector-system.svc.cluster.local
            port:
              number: 8443
          headers:
            response:
              set:
                Strict-Transport-Security: max-age=31536000; includeSubDomains
          weight: 100
      timeout: 10s
```

# TLS PASSTHROUGH with Neuvector UI

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
        - neuvector.10-7-2-67.sslip.io
      port:
        name: https
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
  name: neuvector-vs
  namespace: cattle-neuvector-system
spec:
  gateways:
    - istio-system/public-gateway
  hosts:
    - neuvector.10-7-2-67.sslip.io
  tls:
    - match:
        - port: 443
          sniHosts:
            - neuvector.10-7-2-67.sslip.io
      route:
        - destination:
            host: neuvector-service-webui.cattle-neuvector-system.svc.cluster.local
            port:
              number: 8443
          weight: 100
```
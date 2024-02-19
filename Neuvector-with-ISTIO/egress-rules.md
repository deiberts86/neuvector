```yaml
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: public-egress-gw
  namespace: istio-system
spec:
  selector:
    app: istio-egressgateway
  servers:
    - hosts:
        - '*'
      port:
        name: tls-egress
        number: 443
        protocol: TLS
      tls:
        mode: PASSTHROUGH
---
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: nv-harbor-destrule
  namespace: istio-system
spec:
  host: istio-egressgateway.istio-system.svc.cluster.local
  subsets:
    - name: harbor
---
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: nv-mcmcluster-destrule
  namespace: istio-system
spec:
  host: istio-egressgateway.istio-system.svc.cluster.local
  subsets:
    - name: nv-federated-mcmcluster
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: egress-vs-harbor
  namespace: cattle-neuvector-system
spec:
  gateways:
    - mesh
    - istio-system/public-egress-gw
  hosts:
    - harbor.10-7-2-66.sslip.io
  tls:
    - match:
        - gateways:
            - mesh
          port: 443
          sniHosts:
            - harbor.10-7-2-66.sslip.io
      route:
        - destination:
            host: istio-egressgateway.istio-system.svc.cluster.local
            port:
              number: 443
            subset: harbor
          weight: 100
    - match:
        - gateways:
            - istio-system/public-egress-gw
          port: 443
          sniHosts:
            - harbor.10-7-2-66.sslip.io
      route:
        - destination:
            host: harbor.10-7-2-66.sslip.io
            port:
              number: 443
          weight: 100
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: egress-vs-nv-federated-mcmcluster
  namespace: cattle-neuvector-system
spec:
  gateways:
    - mesh
    - istio-system/public-egress-gw
  hosts:
    - neuvector-fed-managed.10-7-2-158.sslip.io
  tls:
    - match:
        - gateways:
            - mesh
          port: 443
          sniHosts:
            - neuvector-fed-managed.10-7-2-158.sslip.io
      route:
        - destination:
            host: istio-egressgateway.istio-system.svc.cluster.local
            port:
              number: 443
            subset: nv-federated-mcmcluster
          weight: 100
    - match:
        - gateways:
            - istio-system/public-egress-gw
          port: 443
          sniHosts:
            - neuvector-fed-managed.10-7-2-158.sslip.io
      route:
        - destination:
            host: neuvector-fed-managed.10-7-2-158.sslip.io
            port:
              number: 443
          weight: 100
---
apiVersion: networking.istio.io/v1beta1
kind: ServiceEntry
metadata:
  name: harbor-svc-entry
  namespace: cattle-neuvector-system
spec:
  hosts:
    - harbor.10-7-2-66.sslip.io
  location: MESH_EXTERNAL
  ports:
    - name: https
      number: 443
      protocol: TLS
  resolution: DNS
---
apiVersion: networking.istio.io/v1beta1
kind: ServiceEntry
metadata:
  name: nv-fed-mcmcluster
  namespace: cattle-neuvector-system
spec:
  hosts:
    - neuvector-fed-managed.10-7-2-158.sslip.io
  location: MESH_EXTERNAL
  ports:
    - name: https
      number: 443
      protocol: TLS
  resolution: DNS
```
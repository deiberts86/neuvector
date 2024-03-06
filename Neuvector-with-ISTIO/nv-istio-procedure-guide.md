# NeuVector Install with ISTIO Integration

Requirements:
- kubectl
- helm
- active RKE2 or K3s kubernetes cluster with sufficient resources
- Rancher MCM with Rancher Charts Enabled

## Pre-seed Namespaces

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: cattle-neuvector-system
  labels:
    istio-injection: enabled
    pod-security.kubernetes.io/audit: restricted
    pod-security.kubernetes.io/audit-version: latest
    pod-security.kubernetes.io/enforce: privileged
    pod-security.kubernetes.io/enforce-version: latest
    pod-security.kubernetes.io/warn: restricted
    pod-security.kubernetes.io/warn-version: latest
```

## Install Cert-Manager

```yaml
helm upgrade -i \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.13.0 \
  --set installCRDs=true
```

### Create Cluster Issuer via Cert-Manager
Cert-Manager clusterIssuer is what will serve your self-signed certificates or bring your own CA cert

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: selfsigned-issuer
spec:
  selfSigned: {}
---
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: dev-cluster-ca
  namespace: cert-manager
spec:
  isCA: true
  commonName: dev-cluster-ca
  secretName: root-secret
  privateKey:
    algorithm: ECDSA
    size: 256
  issuerRef:
    name: selfsigned-issuer
    kind: ClusterIssuer
    group: cert-manager.io
---
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: dev-cluster-issuer
spec:
  ca:
    secretName: root-secret
```

### Create Your Own Certificate From Cluster Issuer

```yaml
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: neuvector-tls-cert
  namespace: istio-system
spec:
  duration: 8760h # 365 days
  renewBefore: 360h # 15 days
  secretName: neuvector-tls-cert
  commonName: neuvector.10-7-2-67.sslip.io
  dnsNames:
    - neuvector.10-7-2-67.sslip.io
  issuerRef:
    name: dev-cluster-issuer
    kind: ClusterIssuer
    group: cert-manager.io
  usages:
    - server auth
    - client auth
## Optional
#   otherNames:
#     # Should only supply oid of ut8 valued types
#     - oid: 1.3.6.1.4.1.311.20.2.3 # User Principal Name "OID"
#       utf8Value: upn@example.local
```

## Install Rancher ISTIO Chart
- Note: LoadBalancer services will be used for both Ingress and Egress Gateways

```yaml
base:
  enabled: true
cni:
  enabled: true
  excludeNamespaces:
    - istio-system
    - kube-system
  logLevel: info
  repository: rancher/mirrored-istio-install-cni
  tag: 1.18.2-distroless
dns:
  enabled: false
egressGateways:
  enabled: true
  hpaSpec: {}
  podDisruptionBudget: {}
  type: LoadBalancer
forceInstall: false
global:
  cattle:
    psp:
      enabled: false
  defaultPodDisruptionBudget:
    enabled: true
  proxy:
    repository: rancher/mirrored-istio-proxyv2
    tag: 1.18.2-distroless
  proxy_init:
    repository: rancher/mirrored-istio-proxyv2
    tag: 1.18.2-distroless
ingressGateways:
  enabled: true
  hpaSpec: {}
  podDisruptionBudget: {}
  type: LoadBalancer
installer:
  debug:
    secondsSleep: 0
  releaseMirror:
    enabled: false
  repository: rancher/istio-installer
  tag: 1.18.2-rancher1
istiodRemote:
  enabled: false
kiali:
  auth:
    strategy: anonymous
  deployment:
    ingress_enabled: false
  enabled: true
  external_services:
    grafana:
      in_cluster_url: http://rancher-monitoring-grafana.cattle-monitoring-system.svc:80
      url: http://rancher-monitoring-grafana.cattle-monitoring-system.svc:80
    prometheus:
      custom_metrics_url: http://rancher-monitoring-prometheus.cattle-monitoring-system.svc:9090
      url: http://rancher-monitoring-prometheus.cattle-monitoring-system.svc:9090
    tracing:
      in_cluster_url: http://tracing.istio-system.svc:16686/jaeger
      use_grpc: false
  server:
    web_root: /
nodeSelector: {}
overlayFile: |
  apiVersion: install.istio.io/v1beta1
  kind: IstioOperator
  spec:
    components:
      cni:
        enabled: true
        k8s:
          overlays:
          - apiVersion: "apps/v1"
            kind: "DaemonSet"
            name: "istio-cni-node"
            patches:
            - path: spec.template.spec.containers.[name:install-cni].securityContext.privileged
              value: true
            - path: spec.template.spec.tolerations
              value:
              - key: node-role.kubernetes.io/master
                operator: Exists
                effect: NoSchedule
              - key: node-role.kubernetes.io/etcd
                operator: Exists
                effect: NoExecute
              - key: node-role.kubernetes.io/control-plane
                operator: Exists
                effect: NoSchedule
    values:
      cni:
        cniBinDir: /opt/cni/bin
        cniConfDir: /etc/cni/net.d
pilot:
  enabled: true
  hpaSpec: {}
  podDisruptionBudget: {}
  repository: rancher/mirrored-istio-pilot
  tag: 1.18.2-distroless
tag: 1.18.2
telemetry:
  enabled: true
  v2:
    enabled: true
tolerations: []
tracing:
  contextPath: /jaeger
  enabled: true
```

## Install Rancher NeuVector Chart (Manager HTTPS disabled)
- Current user supplied values:
- paste this in your values.yaml section within Rancher MCM UI for Neuvector or build your own values.yaml file and apply through CLI with Helm.
- This is for SSL Termination at the Edge of ISTIO (Recommended)

```yaml
controller:
  federation:
    mastersvc:
      ingress:
        enabled: false
        host: neuvector-fed-master.10-7-2-67.sslip.io
        tls: false
      type: ClusterIP
    managedsvc:
     ingress:
       enabled: false
       host: neuvector-fed-managed.10-7-2-67.sslip.io
       tls: false
     type: ClusterIP
  pvc:
    enabled: true
    storageClass: longhorn
  ranchersso:
    enabled: false
cve:
  adapter:
    enabled: true
    svc:
      type: ClusterIP
  scanner:
    runAsUser: 1000
  updater:
    podAnnotations:
      #traffic.sidecar.istio.io/excludeOutboundIPRanges: "*"
      sidecar.istio.io/inject: 'false'
    runAsUser: 1000
docker:
  enabled: false
enforcer:
  tolerations:
  - effect: NoSchedule
    key: node-role.kubernetes.io/master
  - effect: NoSchedule
    key: node-role.kubernetes.io/control-plane
  - effect: NoExecute
    key: node-role.kubernetes.io/etcd
global:
  cattle:
    clusterName: harvester
    url: https://rancher.10-7-2-160.sslip.io
  systemDefaultRegistry: ""
k3s:
  enabled: true
manager:
  env:
    ssl: false
    envs:
    - name: CUSTOM_PAGE_HEADER_COLOR
      value: '#007a33'
    - name: CUSTOM_PAGE_HEADER_CONTENT
      value: Y2xlYXJlZAo=
    - name: CUSTOM_PAGE_FOOTER_COLOR
      value: '#007a33'
    - name: CUSTOM_PAGE_FOOTER_CONTENT
      value: Y2xlYXJlZAo=
    - name: CUSTOM_LOGIN_LOGO
      value: # YOUR BASE64'ed .SVG logo here!
    - name: CUSTOM_EULA_POLICY
      value: # YOUR BASE64'ed EULA Banner Here!
  runAsUser: 1000
  svc:
    type: ClusterIP
```

### Install Rancher NeuVector Chart (Manager HTTPS enabled)
- Current user supplied values:
- paste this in your values.yaml section within Rancher MCM UI for Neuvector or build your own values.yaml file and apply through CLI with Helm.
- This is for SSL PASSTHROUGH the Ingress Gateway and terminate at the POD.

```yaml
controller:
  federation:
    managedsvc:
      type: ClusterIP
    mastersvc:
      type: ClusterIP
  pvc:
    enabled: true
    storageClass: longhorn
  ranchersso:
    enabled: false
cve:
  adapter:
    enabled: true
    svc:
      type: ClusterIP
  scanner:
    runAsUser: 1000
  updater:
    podAnnotations:
      traffic.sidecar.istio.io/excludeOutboundIPRanges: '*'
    runAsUser: 1000
docker:
  enabled: false
enforcer:
  tolerations:
  - effect: NoSchedule
    key: node-role.kubernetes.io/master
  - effect: NoSchedule
    key: node-role.kubernetes.io/control-plane
  - effect: NoExecute
    key: node-role.kubernetes.io/etcd
global:
  cattle:
    clusterName: harvester
    url: https://rancher.10-7-2-160.sslip.io
  systemDefaultRegistry: ""
k3s:
  enabled: true
manager:
  env:
    ssl: true
  runAsUser: 1000
  svc:
    type: ClusterIP
```

### Create ISTIO Gateway and Virtual Service (uses Default ISTIO GW) with TLS Termination at Edge
- Provided certificate is what will be used to terminate at the edge. Ensure the backend service is NOT HTTPS otherwise you will be doing double-TLS which will not work. Meaning your application (Neuvector should )

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
        credentialName: neuvector-tls-cert
        maxProtocolVersion: TLSV1_3
        minProtocolVersion: TLSV1_2
        mode: SIMPLE
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
  http:
    - match:
        - port: 443
        - uri:
            prefix: /
      name: https
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
      timeout: 15s
---
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: neuvector-destrule
  namespace: cattle-neuvector-system
spec:
  host: neuvector-service-webui.cattle-neuvector-system.svc.cluster.local
  trafficPolicy:
    loadBalancer:
      simple: ROUND_ROBIN
```

### SNI Passthrough NeuVector
- Note: With SNI passthrough, Istio does not terminate TLS connections at the gateway, which means Istio does not have access to the traffic's plaintext and cannot perform mutual TLS (mTLS) authentication. SNI passthrough is designed to forward TLS connections directly to the backend service without decryption at the gateway, preserving end-to-end encryption.

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
---

![This looks neat](/images/Screenshot%202024-02-19%20at%206.09.27%20PM.png)

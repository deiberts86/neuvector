# Install Standard Neuvector
- Still in progress on the docs.

Helm User provided values for Neuvector:
```yaml
controller:
  federation:
    managedsvc:
      ingress:
        host: neuvector-fed-managed.10-7-2-67.sslip.io
      type: ClusterIP
    mastersvc:
      ingress:
        host: neuvector-fed-master.10-7-2-67.sslip.io
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
    runAsUser: 1000
    secure: true
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
    clusterId: c-m-cmw7tm2r
    clusterName: harvester
    rkePathPrefix: ""
    rkeWindowsPathPrefix: ""
    systemDefaultRegistry: ""
    systemProjectId: p-nb4ps
    url: https://rancher.10-7-2-160.sslip.io
  systemDefaultRegistry: ""
k3s:
  enabled: true
manager:
  env:
    envs:
    - name: CUSTOM_PAGE_HEADER_COLOR
      value: '#007a33'
    - name: CUSTOM_PAGE_HEADER_CONTENT
      value: VS8vRk9VTwo=
    - name: CUSTOM_PAGE_FOOTER_COLOR
      value: '#007a33'
    - name: CUSTOM_PAGE_FOOTER_CONTENT
      value: VS8vRk9VTwo=
    - name: CUSTOM_LOGIN_LOGO
      value: # YOUR BASE64'ed .SVG logo here!
    - name: CUSTOM_EULA_POLICY
      value: # YOUR BASE64'ed EULA Banner Here!
    ssl: true
  runAsUser: 1000
  svc:
    type: ClusterIP
```
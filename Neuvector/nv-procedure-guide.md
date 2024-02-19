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
      value: PD94bWwgdmVyc2lvbj0iMS4wIiBzdGFuZGFsb25lPSJubyI/Pgo8IURPQ1RZUEUgc3ZnIFBVQkxJQyAiLS8vVzNDLy9EVEQgU1ZHIDIwMDEwOTA0Ly9FTiIKICJodHRwOi8vd3d3LnczLm9yZy9UUi8yMDAxL1JFQy1TVkctMjAwMTA5MDQvRFREL3N2ZzEwLmR0ZCI+CjxzdmcgdmVyc2lvbj0iMS4wIiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciCiB3aWR0aD0iMzAwLjAwMDAwMHB0IiBoZWlnaHQ9IjgwLjAwMDAwMHB0IiB2aWV3Qm94PSIwIDAgMzAwLjAwMDAwMCA4MC4wMDAwMDAiCiBwcmVzZXJ2ZUFzcGVjdFJhdGlvPSJ4TWlkWU1pZCBtZWV0Ij4KPG1ldGFkYXRhPgpDcmVhdGVkIGJ5IHBvdHJhY2UgMS4xNiwgd3JpdHRlbiBieSBQZXRlciBTZWxpbmdlciAyMDAxLTIwMTkKPC9tZXRhZGF0YT4KPGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoMC4wMDAwMDAsODAuMDAwMDAwKSBzY2FsZSgwLjA2OTQ0NCwtMC4wNjgzNzYpIgpmaWxsPSIjMDAwMDAwIiBzdHJva2U9Im5vbmUiPgo8cGF0aCBkPSJNMzc2NSAxMTUxIGMtMTIxIC0zMiAtMjA1IC0xMzIgLTIwNSAtMjQ1IDAgLTEwNCA2OSAtMTg2IDE5NSAtMjMxCjQ0IC0xNSAxMDQgLTM3IDEzNCAtNDggNjcgLTI0IDEwMSAtNTYgMTAxIC05NSAwIC00NCAtMjggLTcyIC03NyAtNzkgLTc3IC0xMAotMTMzIDM1IC0xMzMgMTA3IDAgMTkgLTYgMjAgLTEwNSAyMCAtMTIyIDAgLTEyNCAtMSAtMTA1IC04MiAyNiAtMTExIDk2IC0xNzUKMjI3IC0yMDQgMjQ5IC01NSA0NTkgMTA5IDQwNCAzMTQgLTI0IDg2IC04MCAxMjcgLTI2NCAxOTYgLTEyMCA0NSAtMTMzIDUzCi0xNDggODggLTI4IDY5IDQ4IDEyNiAxMjUgOTQgMzggLTE2IDU2IC00MiA1NiAtODAgbDAgLTI2IDExNCAwIDExMyAwIC04IDQ2CmMtMTkgMTAyIC03MyAxNzQgLTE1NyAyMTAgLTQxIDE4IC0yMTggMjggLTI2NyAxNXoiLz4KPHBhdGggZD0iTTg1MCAxMDkwIGwwIC02MCA0NzUgMCA0NzUgMCAwIDYwIDAgNjAgLTQ3NSAwIC00NzUgMCAwIC02MHoiLz4KPHBhdGggZD0iTTE5MTAgNzIwIGwwIC00MzAgMTA1IDAgMTA1IDAgMCAxNjAgMCAxNjAgMjggMCBjMjUgLTEgMzQgLTE0IDExMgotMTU4IGw4NSAtMTU3IDExOCAtMyBjNjQgLTEgMTE3IC0xIDExNyAyIDAgMiAtNDMgNzcgLTk1IDE2NyAtNTIgODkgLTk1IDE2MwotOTUgMTY0IDAgMiAyNSAxNSA1NSAzMCAxNTcgNzYgMTgxIDMxNyA0MyA0MjYgLTc0IDYwIC0xMjUgNjkgLTM2NSA2OSBsLTIxMwowIDAgLTQzMHogbTQwMCAyNDIgYzI2IC0xMiAzOSAtMjggNTAgLTYwIDE0IC00MCAxMyAtNDUgLTUgLTgwIC0yNyAtNTAgLTU2Ci02MiAtMTUyIC02MiBsLTgzIDAgMCAxMTAgMCAxMTAgNzggMCBjNTMgMCA4OCAtNSAxMTIgLTE4eiIvPgo8cGF0aCBkPSJNMjY4MCAxMTQwIGMwIC0yNCAyOSAtNzggNTAgLTkzIGwyMiAtMTYgLTMxIC00MyBjLTk1IC0xMzMgLTEwNwotMzM4IC0yOSAtNDkyIDg4IC0xNzMgMzMyIC0yNjAgNTMzIC0xOTEgNjIgMjEgMTU2IDg2IDE5MCAxMzEgNTcgNzQgNzUgMTMxCjc1IDIzNyBsMCA5NyAtMjI1IDAgLTIyNSAwIDAgLTc1IDAgLTc1IDEzMCAwIGM3MiAwIDEzMCAtMyAxMzAgLTcgMCAtMyAtMTEKLTI2IC0yNSAtNDkgLTQ5IC04NCAtMTU0IC0xMjAgLTI1OCAtOTAgLTY2IDE5IC0xMTAgNTMgLTE0MCAxMTAgLTMxIDU5IC0zMgoyMDMgMCAyNjEgMzYgNjggOTEgMTA3IDE2OSAxMTggNTYgOSAxMzMgLTE1IDE2OSAtNTMgMjkgLTMwIDI5IC0zMCAxNDMgLTMwCjExMyAwIDExMyAwIDEwNiAyMyAtMzIgMTA2IC0xMDggMTgyIC0yMjcgMjI2IC01MyAxOSAtNzcgMjEgLTMwNyAyMSAtMTY1IDAKLTI1MCAtNCAtMjUwIC0xMHoiLz4KPHBhdGggZD0iTTQ3NyAxMTI2IGMtNCAtMTAgLTcgLTMzIC03IC01MiAwIC0zMyAxIC0zNCA0MiAtMzQgMzAgMCAzOSAtMyAzMQotMTEgLTYgLTYgLTI4IC05IC00OSAtNyAtMzggMyAtMzkgNCAtNDIgNDYgbC0zIDQyIC0xODQgMCAtMTg0IDAgLTM2IC01MgpjLTMyIC00OCAtNDMgLTc4IC0zMCAtNzggMyAwIDE3IDcgMzAgMTYgbDI1IDE2IDAgLTExNiAwIC0xMTYgNjQgMCA2NSAwIDMgNTMKMyA1MiAxNDAgMCAxNDAgMCA1IC01MCA1IC01MCA2MyAtMyA2MiAtMyAwIDUwIDAgNTAgLTM3IDMgYy0zOCAzIC0zOCAzIC00MQo1MSAtMiAyOCAxIDQ3IDcgNDcgNiAwIDExIC0xOCAxMSAtMzkgMCAtNDMgMTAgLTUxIDY4IC01MSA2MCAwIDYzIDUgNjAgMTE2CmwtMyA5OSAtODkgMyBjLTY4IDIgLTkyIDcgLTEwMSAxOSAtMTEgMTQgLTEyIDE0IC0xOCAtMXoiLz4KPHBhdGggZD0iTTg1MCA4NDAgbDAgLTYwIDQ3NSAwIDQ3NSAwIDAgNjAgMCA2MCAtNDc1IDAgLTQ3NSAwIDAgLTYweiIvPgo8cGF0aCBkPSJNNDYzIDY1MyBsLTQ1MyAtMyAwIC01NSAwIC01NSA4OTUgMCA4OTUgMCAwIDYwIDAgNjAgLTQ0MiAtMiBjLTI0NAotMiAtNjQ3IC00IC04OTUgLTV6Ii8+CjxwYXRoIGQ9Ik0xMCAzNTAgbDAgLTYwIDg5NSAwIDg5NSAwIDAgNjAgMCA2MCAtODk1IDAgLTg5NSAwIDAgLTYweiIvPgo8cGF0aCBkPSJNMTAgMTA1IGMwIC03OCAzIC05NSAxNSAtOTUgMTEgMCAxNSAxMSAxNSA0MCAwIDUzIDIyIDUzIDUxIDAgMTMKLTIyIDI5IC00MCAzNyAtNDAgMTEgMCA5IDggLTcgMzYgbC0yMiAzNyAyMSAyMiBjNDUgNDggMTUgOTUgLTYxIDk1IGwtNDkgMCAwCi05NXoiLz4KPHBhdGggZD0iTTE1MDQgMTU4IGMtNDcgLTIyIC01NiAtODYgLTE5IC0xMjMgMzkgLTQwIDk2IC0zMiAxMjEgMTYgMTggMzYgMTAKNzMgLTIzIDk5IC0yOCAyMiAtNDcgMjQgLTc5IDh6Ii8+CjxwYXRoIGQ9Ik0zMjY1IDE0NSBjLTUwIC00OSAtMjAgLTEyOSA0OCAtMTI5IDQ5IDAgNzcgMjcgNzcgNzUgMCA3MCAtNzcgMTAzCi0xMjUgNTR6Ii8+CjxwYXRoIGQ9Ik0zODY2IDE0OSBjLTMxIC0yNSAtMzUgLTc1IC04IC0xMDkgMjUgLTMxIDg5IC0zMSAxMTQgMCAyNyAzNCAyMyA4NAotOCAxMDkgLTE1IDEyIC0zNyAyMSAtNDkgMjEgLTEyIDAgLTM0IC05IC00OSAtMjF6Ii8+CjxwYXRoIGQ9Ik0yMDQgMTUyIGMtMTYgLTEwIC0zNCAtNDggLTM0IC03MCAwIC0zNSAzOCAtNzIgNzQgLTcyIDE3IDAgMzcgNSA0NAoxMiA5IDkgMTIgOSAxMiAwIDAgLTcgNyAtMTIgMTUgLTEyIDEyIDAgMTUgMTUgMTUgNzUgMCA2MCAtMyA3NSAtMTUgNzUgLTggMAotMTUgLTUgLTE1IC0xMiAwIC05IC0zIC05IC0xMiAwIC0xMyAxMyAtNjcgMTYgLTg0IDR6Ii8+CjwvZz4KPC9zdmc+Cg==
    - name: CUSTOM_EULA_POLICY
      value: PGI+WW91IGFyZSBhY2Nlc3NpbmcgYSBVLlMuIEdvdmVybm1lbnQgKFVTRykgSW5mb3JtYXRpb24gU3lzdGVtIChJUykgdGhhdCBpcyBwcm92aWRlZCBmb3IgVVNHLWF1dGhvcml6ZWQgdXNlIG9ubHkuPC9iPjxicj4KPGJyPgpCeSB1c2luZyB0aGlzIElTICh3aGljaCBpbmNsdWRlcyBhbnkgZGV2aWNlIGF0dGFjaGVkIHRvIHRoaXMgSVMpLCB5b3UgY29uc2VudCB0byB0aGUgZm9sbG93aW5nIGNvbmRpdGlvbnM6PGJyPgo8YnI+Ci1UaGUgVVNHIHJvdXRpbmVseSBpbnRlcmNlcHRzIGFuZCBtb25pdG9ycyBjb21tdW5pY2F0aW9ucyBvbiB0aGlzIElTIGZvciBwdXJwb3NlcyBpbmNsdWRpbmcsIGJ1dCBub3QgbGltaXRlZCB0bywgcGVuZXRyYXRpb24gdGVzdGluZywgQ09NU0VDIG1vbml0b3JpbmcsIG5ldHdvcmsgb3BlcmF0aW9ucyBhbmQgZGVmZW5zZSwgcGVyc29ubmVsIG1pc2NvbmR1Y3QgKFBNKSwgbGF3IGVuZm9yY2VtZW50IChMRSksIGFuZCBjb3VudGVyaW50ZWxsaWdlbmNlIChDSSkgaW52ZXN0aWdhdGlvbnMuPGJyPgo8YnI+Ci1BdCBhbnkgdGltZSwgdGhlIFVTRyBtYXkgaW5zcGVjdCBhbmQgc2VpemUgZGF0YSBzdG9yZWQgb24gdGhpcyBJUy48YnI+Cjxicj4KLUNvbW11bmljYXRpb25zIHVzaW5nLCBvciBkYXRhIHN0b3JlZCBvbiwgdGhpcyBJUyBhcmUgbm90IHByaXZhdGUsIGFyZSBzdWJqZWN0IHRvIHJvdXRpbmUgbW9uaXRvcmluZywgaW50ZXJjZXB0aW9uLCBhbmQgc2VhcmNoLCBhbmQgbWF5IGJlIGRpc2Nsb3NlZCBvciB1c2VkIGZvciBhbnkgVVNHLWF1dGhvcml6ZWQgcHVycG9zZS48YnI+Cjxicj4KLVRoaXMgSVMgaW5jbHVkZXMgc2VjdXJpdHkgbWVhc3VyZXMgKGUuZy4sIGF1dGhlbnRpY2F0aW9uIGFuZCBhY2Nlc3MgY29udHJvbHMpIHRvIHByb3RlY3QgVVNHIGludGVyZXN0cy0tbm90IGZvciB5b3VyIHBlcnNvbmFsIGJlbmVmaXQgb3IgcHJpdmFjeS48YnI+Cjxicj4KLU5vdHdpdGhzdGFuZGluZyB0aGUgYWJvdmUsIHVzaW5nIHRoaXMgSVMgZG9lcyBub3QgY29uc3RpdHV0ZSBjb25zZW50IHRvIFBNLCBMRSBvciBDSSBpbnZlc3RpZ2F0aXZlIHNlYXJjaGluZyBvciBtb25pdG9yaW5nIG9mIHRoZSBjb250ZW50IG9mIHByaXZpbGVnZWQgY29tbXVuaWNhdGlvbnMsIG9yIHdvcmsgcHJvZHVjdCwgcmVsYXRlZCB0byBwZXJzb25hbCByZXByZXNlbnRhdGlvbiBvciBzZXJ2aWNlcyBieSBhdHRvcm5leXMsIHBzeWNob3RoZXJhcGlzdHMsIG9yIGNsZXJneSwgYW5kIHRoZWlyIGFzc2lzdGFudHMuIFN1Y2ggY29tbXVuaWNhdGlvbnMgYW5kIHdvcmsgcHJvZHVjdCBhcmUgcHJpdmF0ZSBhbmQgY29uZmlkZW50aWFsLiBTZWUgVXNlciBBZ3JlZW1lbnQgZm9yIGRldGFpbHMuIDxicj4gCg==
    ssl: true
  runAsUser: 1000
  svc:
    type: ClusterIP
```
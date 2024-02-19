# ISTIO Tracing with Jaegar integration
- [envoyFilters](https://istio.io/latest/docs/reference/config/networking/envoy-filter/)

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: EnvoyFilter
metadata:
  name: access-log
  namespace: istio-system
spec:
  configPatches:
    - applyTo: NETWORK_FILTER
      match:
        context: ANY
        listener:
          filterChain:
            filter:
              name: envoy.filters.network.http_connection_manager
      patch:
        operation: MERGE
        value:
          typed_config:
            '@type': >-
              type.googleapis.com/envoy.extensions.filters.network.http_connection_manager.v3.HttpConnectionManager
            access_log:
              - name: envoy.file_access_log
                typed_config:
                  '@type': >-
                    type.googleapis.com/envoy.extensions.access_loggers.file.v3.FileAccessLog
                  format: >
                    [%START_TIME%] "%REQ(:METHOD)%
                    %REQ(X-ENVOY-ORIGINAL-PATH?:PATH)% %PROTOCOL%"
                    %RESPONSE_CODE% %RESPONSE_FLAGS% 
                  path: /dev/stdout
            tracing:
              random_sampling:
                value: 100
---
apiVersion: networking.istio.io/v1alpha3
kind: EnvoyFilter
metadata:
  name: http-envoy-tracing
  namespace: istio-system
spec:
  configPatches:
    - applyTo: HTTP_ROUTE
      match:
        context: GATEWAY
        routeConfiguration:
          portNumber: 80
          vhost:
            name: '*:80'
      patch:
        operation: MERGE
        value:
          tracing:
            random_sampling:
              denominator: TEN_THOUSAND
              numerator: 9990
---
apiVersion: networking.istio.io/v1alpha3
kind: EnvoyFilter
metadata:
  name: https-envoy-tracing
  namespace: istio-system
spec:
  configPatches:
    - applyTo: HTTP_ROUTE
      match:
        context: GATEWAY
        routeConfiguration:
          portNumber: 443
          vhost:
            name: '*:443'
      patch:
        operation: MERGE
        value:
          tracing:
            random_sampling:
              denominator: TEN_THOUSAND
              numerator: 9990
```

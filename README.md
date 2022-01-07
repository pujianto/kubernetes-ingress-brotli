# nginx-ingress-brotli
Add Brotli Compression to the kubernetes-ingress image


## To enable this feature
#### 1. Edit your `nginx-ingress.yaml` 
from:
```
...
spec:
  serviceAccountName: nginx-ingress
  containers:
  - image: nginx/nginx-ingress:2.1.0

    imagePullPolicy: IfNotPresent
    name: nginx-ingress
        ...
```
to this:
```
spec:
  serviceAccountName: nginx-ingress
  containers:
  - image: pujianto/nginx-ingress-brotli:2.1.0
    imagePullPolicy: IfNotPresent
    name: nginx-ingress
```
#### 2. Update your `nginx-config` configMap
Make sure nginx to load the `.so` brotli modules via `main-snippets` in your nginx-config configMap
```
apiVersion: v1
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: nginx-config
  namespace: nginx-ingress
data:
  http2: "true"
  main-snippets: |
    load_module "modules/ngx_http_brotli_filter_module.so";
    load_module "modules/ngx_http_brotli_static_module.so";    
```

### 3. Now you can load brotli module from your ingress resources
You can enable this using `server-snippets`

Example:
```
...
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: your-ingress
  annotations:
    nginx.org/server-snippets: |
      brotli on;
      brotli_static on;
 ...
```
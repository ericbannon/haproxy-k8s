## HAProxy and Traefik Ingress for Web Service Blue Print 

* SSL Pass-Through and HTTPS
* Sticky Sessions 
* Separate reads/writes for Database Traffic 

### SSL Pass-Through and TLS

        args:
          - --configmap=default/haproxy-configmap
          - --default-backend-service=haproxy-controller/ingress-default-backend
          - --default-ssl-certificate=default/tls-secret
          - --configmap=$(POD_NAMESPACE)/haproxy-configmap
          - --reload-strategy=native

A TLS secret should be created and  maintained in the default namespace. Enabling this argument allows use of SSL with the Ingress Controller by using an ad-hoc TLS secret in Kubernetes. The Ingress object itself dictates which certs are used by an Ingress object during ingress through HA Proxy 
        
The TLS flag in “spec” secures Ingress by specifying a Secret that contains a TLS private key and certificate. Currently the Ingress api in Kubernetes only supports a single TLS port, 443, and assumes TLS termination. If the TLS configuration section in an Ingress specifies different hosts, they are multiplexed on the same port according to the hostname specified through the SNI TLS extension (provided the Ingress controller supports SNI). The TLS secret must contain keys named tls.crt and tls.key that contain the certificate and private key to use for TLS.

All SSL certificates must be  created as Secrets inside the Kubernetes cluster for reference in the ingress spec 

        default-token                               
        tls-secret                               

### Sticky Sessions 

       metadata:
        name: tls-web-ingress
         namespace: web
         annotations:
           haproxy.org/forwarded-for: "enabled"
           haproxy.org/load-balance: "roundrobin"
           haproxy.org/whitelist: "127.0.0.1,192.168.50.1/24"
           haproxy.org/whiteist-with-rate-limit: "ON"
           ingress.kubernetes.io/affinity: "cookie"
           ingress.kubernetes.io/session-cookie-name: "JSESSIONID"

With this enabled, cookie-based persistenceis configured in a backend corresponding to the annotated Ingress resource. If you use the cookie affinity type you can specify the name of the cookie that will be used to route the requests with the annotation. In this case, the name is JESSIONID 

### NFS Separate Reads and Writes - HAProxy

To be added... 

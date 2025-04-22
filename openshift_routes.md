# Routing in OpenShift Container Platform

## OpenShift Routing Overview

OpenShift Container Platform (OCP) provides a robust routing mechanism to expose services running inside your cluster to external clients. Below is a detailed explanation:

### What is Routing in OpenShift?

Routing in OpenShift refers to the process of exposing services to external traffic outside the cluster. It is OpenShift's implementation of ingress, enabling HTTP/HTTPS access to containerized applications.

### Key Components

- **Router**: Handles incoming traffic (deployed as pods).
- **Route**: Defines how traffic is directed to services.
- **Service**: Acts as an internal load balancer, directing traffic to pods.

### How Routing Works

1. A `Route` object is created, specifying:
    - Hostname (e.g., `myapp.example.com`).
    - Target service.
    - Optional TLS settings.
2. The OpenShift router watches for `Route` objects and configures itself.
3. Incoming traffic is processed as follows:
    - Router receives the request.
    - Examines the host/path.
    - Forwards the request to the appropriate service.
    - Service load balances to the backing pods.

### Types of Routes

#### Unsecured Routes: Basic HTTP traffic

```yaml
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: myroute
spec:
  host: myapp.example.com
  to:
     kind: Service
     name: my-service
```

#### Edge-terminated TLS: TLS termination at the router

```yaml
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: myroute
spec:
  host: myapp.example.com
  to:
     kind: Service
     name: my-service
  tls:
     termination: edge
     key: |-
        -----BEGIN PRIVATE KEY-----
        ...
     certificate: |-
        -----BEGIN CERTIFICATE-----
        ...
```

#### Passthrough TLS: TLS traffic passes through to the pod

```yaml
spec:
  tls:
     termination: passthrough
```

#### Re-encrypt TLS: TLS termination at router with new encryption to pod

```yaml
spec:
  tls:
     termination: reencrypt
     destinationCACertificate: |-
        -----BEGIN CERTIFICATE-----
        ...
```

### Advanced Routing Features

- **Path-based routing**: Direct traffic based on URL paths.
  ```yaml
  spec:
     path: /admin
  ```
- **Wildcard routes**: `*.example.com`.
- **Weighted routing**: Split traffic between services (e.g., canary deployments).
  ```yaml
  spec:
     to:
        kind: Service
        name: service-v1
        weight: 80
     alternateBackends:
     - kind: Service
        name: service-v2
        weight: 20
  ```
- **HTTP headers manipulation**: Add, remove, or modify headers.
- **Request timeouts**: Configure response wait times.

### Router Sharding

For large deployments, router sharding can:
- Distribute load across multiple routers.
- Isolate routes for different teams/projects.
- Enable geographic routing.

### Default Router Configuration

OpenShift uses HAProxy as the default router but supports other ingress controllers like NGINX or F5.

---

## Routing in Cloud vs On-Prem Deployments

### Cloud Deployments (AWS/Azure)

#### Default Behavior
When using installer-provisioned infrastructure (IPI):
- An external cloud load balancer (AWS ELB/ALB or Azure LB) is automatically created.
- The load balancer sits in front of OpenShift router pods and is configured with health checks and scaling.

#### Architecture
```
Internet → Cloud LB (AWS ELB/Azure LB) → OpenShift Router Pods (HAProxy) → Services → Pods
```

#### DNS Configuration
- The cloud LB gets a public hostname (e.g., `a1234567890.us-east-1.elb.amazonaws.com`).
- Configure your DNS (e.g., Route53, Azure DNS) to point your domain to this LB.

### On-Premises Deployments

#### Key Differences
- No automatic LB provisioning.
- Manual setup of external load balancers is required.

#### Common Options
- Hardware load balancers (e.g., F5 BIG-IP, Citrix ADC).
- Software load balancers (e.g., HAProxy, NGINX, MetalLB for bare metal).
- Keepalived for high availability.

#### Architecture
```
Internet → Corporate Firewall → External LB → OpenShift Router Pods → Services → Pods
```

#### Configuration Details
- **Cloud Deployments**:
  - LB listens on ports 80 and 443.
  - Forwards traffic to worker nodes (NodePort or HostNetwork mode).
- **On-Prem Manual Setup**:
  - Deploy and configure your LB solution.
  - Example HAProxy configuration:
     ```yaml
     frontend http-in
        bind *:80
        bind *:443
        default_backend routers

     backend routers
        balance roundrobin
        option httpchk GET /healthz
        server openshift-node1 <node1-ip>:<node-port> check
        server openshift-node2 <node2-ip>:<node-port> check
     ```
  - Configure DNS to point to the LB's VIP.

#### Hybrid Approach
- Use solutions like MetalLB, Keepalived + HAProxy, or OpenShift with KubeVirt for automation.

### Verifying Your Setup

- **Cloud Deployments**:
  ```sh
  oc get svc router-default -n openshift-ingress
  # Look for EXTERNAL-IP pointing to your cloud LB
  ```
- **On-Prem**:
  ```sh
  oc get pods -n openshift-ingress -o wide
  # Note the nodes where router pods run, then configure LB to target these
  ```

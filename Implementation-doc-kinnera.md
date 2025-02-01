## ESGBook

# Stack/Tools used:
1) Github Actions  - CI
2) Go App with ```/ping``` and ```/metrics``` routes
3) docker       - container runtime used, other alternaives are containerd.
4) kubernetes   - orchestrator

## Testing in local/dev env:
- ```SERVICE__PORT=8080 TARGET=http://localhost:8080/ping  go run .```                #start and test app
- ```CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -a -o pingpong```                 #now build executable
- ```docker build -t pingpong . -f Dockerfile.multistage```                           #build image
- ```docker run -p 8080:8080 -p 9080:9080 --env-file .env  pingpong```                 #run image
- Metrics:
- Added ```ping_requests_received_total``` and ```ping_failures_total``` metrics in metrics.go and modified startAppServer function in main.go accordingly
- Go to ```http://localhost:9080/-/metrics``` to view the metrics
- Simulating a failure request to observe ```ping_failures_total``` metrics using ```curl -X POST http://localhost:3030/ping```

## Deployment in test/dev namespace in local kubernetes

## Deployment in prod kubernetes cluster

- Highlight the drawback of NetworkPolicies (https://kubernetes.io/docs/concepts/services-networking/network-policies/#what-you-can-t-do-with-network-policies-at-least-not-yet)
The ability to explicitly deny policies (currently the model for NetworkPolicies are deny by default, with only the ability to add allow rules).
block egress to a particular ip/cidr while allowing rest all.
It currently can
block all ingress or egress.
block all and allow only a particular ip/cidr.

Application Deployment:
    Manifest Changes:
        Updated the service to have the right selector and namespace
        Updated the TARGET env variable

Metrics Collection:
    Updated the application code to collect certain metrics in metrics.go and main.go

HTTPS/TLS teramination:
    TLS termination can be done at different levels like on application, CDN, Loadbalancer, Ingress, Istio etc
    
    Application level: Implemented it and can demo it during interview. Need application code change. I would not prefer it as other solutions provide much better security and ease.
    
    Ingress: Implemented it and can demo it during interview. 
    
    Istio:
        Created a Secret with crt and key
        Created a Gateway
        Created a Virtual Service
    Test:
        Port forwarded and hit the target with https 
    
mTLS:
    Implemented it with Istio
    
    Istio:
        Injected istio to the namepsaces(pingpong-a and pingpong-b) to get the envoy proxy/init containers
        Created PeerAuthentication to enable mTLS for both pingpong-a and pingpong-b
    Test:
        Created a test pod in pingpong-c namespace without istio-injection or PeerAuthentication enabled
            1. When sent a request from pingpong-a pod to pingpong-b or vice-versa, the communication works in a secured way(we get a success response)
            2. When sent a request from pingpong-c pod to other two namespaces pods, the communication fails which proves that mTLS is enabled

Few other featues that can be implemented to make it Production ready:
    1. Security
        RBAC
    2. Reliability
        Health Checks (Liveness Probe, Readiness Probe)
        Autoscaling (Pod disruption budgets, Horizontal Pod autoscaling)
        Rolling updates
    3. Observability
        Dashboards for the ease of monitoring
    4. Deployment
        Helm charts for kubernetes objects
        GitOps(ArgoCD)
        CI/CD pipelines 


# Improvements:
- Code is not loading values from ```.env``` file. Maybe koanf library doesn't reads environment variables from your system and not from file or maybe we need to use different library ```godotenv```
- Helm chart can be created 


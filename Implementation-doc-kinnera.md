Application Deployment:
    Created multi stage build Dockerfile in order to have light weight image
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
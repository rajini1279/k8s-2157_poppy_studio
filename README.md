# k8s-2157_poppy_studio
Sample K8s project
# k8s-2157_poppy_studio
Sample K8s project


## Commands executed 
git clone https://github.com/rajini1279/k8s-2157_poppy_studio.git
ls -tlr
git clone https://github.com/rajini1279/k8s-2157_poppy_studio.git
git pull .
git pull
docker build
docker build -t rgundala/exercise:1
docker build -t rgundala/exercise:1 .
docker run -d -p 8090:80 rgundala/exercise:1
git pull
ls -tlr
docker build -t rgundala/exercise:1 -f ./poppy-Dockerfile .
docker build -t rgundala/exercise:2 -f ./542-Dockerfile .
history
To use the access token from your Docker CLI client:

1. Run
ra
docker login -u rgundala

 docker push rgundala/exercise:1
 docker push rgundala/exercise:2

 Interview explanation
startupProbe: Gives the container time to start before health checks begin.
readinessProbe: Controls whether the pod receives traffic from the Service.
livenessProbe: Restarts the container if it becomes unhealthy.



# Explanation of probes 
Interview explanation
startupProbe: Gives the container time to start before health checks begin.
readinessProbe: Controls whether the pod receives traffic from the Service.
livenessProbe: Restarts the container if it becomes unhealthy.

For NGINX serving static HTML, checking GET / on port 80 is the standard and simplest probe configuration.

Can you please explain each configured steps about probs

Let's go through each probe configuration line by line.

Startup Probe
startupProbe:
  httpGet:
    path: /
    port: 80
  periodSeconds: 5
  failureThreshold: 12
httpGet
httpGet:
  path: /
  port: 80

Kubernetes sends an HTTP request to:

http://<pod-ip>:80/

If it gets a successful response (HTTP 200–399), the probe passes.

For your NGINX site, / returns the index.html page.

periodSeconds: 5
periodSeconds: 5

Check every 5 seconds.

Example:

0s   Check #1
5s   Check #2
10s  Check #3
15s  Check #4
...
failureThreshold: 12
failureThreshold: 12

Allow 12 consecutive failures before Kubernetes considers startup failed.

Maximum startup time allowed:

12 × 5 seconds = 60 seconds

So your application gets up to 1 minute to start.

Readiness Probe
readinessProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 5
  periodSeconds: 10
  timeoutSeconds: 2
  failureThreshold: 3

The readiness probe decides:

"Can this pod receive user traffic?"

initialDelaySeconds: 5
initialDelaySeconds: 5

Wait 5 seconds after container start before the first readiness check.

Container starts
      ↓
Wait 5 seconds
      ↓
Start readiness checks
periodSeconds: 10
periodSeconds: 10

Run readiness checks every 10 seconds.

5s   First check
15s  Second check
25s  Third check
...
timeoutSeconds: 2
timeoutSeconds: 2

The HTTP request must complete within 2 seconds.

If the server doesn't respond within 2 seconds:

Probe = Failed
failureThreshold: 3
failureThreshold: 3

After 3 consecutive failures:

Pod removed from Service endpoints

Traffic stops flowing to this pod.

The pod is not restarted.

Liveness Probe
livenessProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 15
  periodSeconds: 20
  timeoutSeconds: 2
  failureThreshold: 3

The liveness probe answers:

"Is the application still alive?"

initialDelaySeconds: 15
initialDelaySeconds: 15

Wait 15 seconds before starting liveness checks.

This prevents Kubernetes from restarting a container that is still booting.

periodSeconds: 20
periodSeconds: 20

Run health checks every 20 seconds.

15s  First liveness check
35s  Second
55s  Third
...
timeoutSeconds: 2
timeoutSeconds: 2

The application must respond within 2 seconds.

Otherwise the check fails.

failureThreshold: 3
failureThreshold: 3

After 3 consecutive failures:

Kubernetes restarts the container

Example:

Check #1 Failed
Check #2 Failed
Check #3 Failed
        ↓
Container Restarted
Complete Lifecycle
Pod Created
     ↓
Container Starts
     ↓
startupProbe
     ↓
Passes
     ↓
readinessProbe
     ↓
Pod added to Service
     ↓
User traffic starts
     ↓
livenessProbe keeps monitoring
     ↓
If app hangs
     ↓
Container restarted
Real Example

Suppose your NGINX container crashes internally:

Browser
   ↓
Service
   ↓
Pod

Readiness may fail first:

Pod removed from Service

No new traffic goes to it.

If it remains unhealthy:

livenessProbe fails 3 times
        ↓
Kubernetes restarts container

This is why:

startupProbe = "Has the app started?"
readinessProbe = "Can it serve traffic?"
livenessProbe = "Should I restart it?"


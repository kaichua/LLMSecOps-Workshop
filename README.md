# LLMSecOps-Workshop

## Overall
The project is managed using the uv python virtual environment.

To start, run the command: `uv sync` to install the neccessary libraries.

To execute commands in the virtual environment, see below:
- To run pip: `uv run pip freeze > requirements.txt`.
- To run python files: `uv run main.py`.

## Docker
### Building Docker Image
Ensure that the terminal is in the same location as your ``Dockerfile``
```
docker build -t fastapi-app .
```

### Running Docker Image
To run the docker image after it has been built execute the following:
```
docker run -p 4000:4000 fastapi-app
```

### Tagging & Pushing Docker Image
To tag the image to be pushed to Docker repository, append the image with the Docker username:
```
docker image tag fastapi-app kaichua95/fastapi-app:latest
```
Afterwards, execute the following to push the image into Docker repository:
```
docker push kaichua95/fastapi-app:latest
```

## Pipeline
### Query
```
curl --location 'localhost:4000/chat' \
--header 'Content-Type: application/json' \
--data '{
    "question": "What does Hugging Face provide?",
    "context": "Hugging Face is a technology company that provides open-source NLP libraries ..."
}'
```

### Response 
``` {"answer":"open-source NLP libraries"} ```

## Vulnerability Scan
### Running aquasec/trivy
```
docker pull aquasec/trivy

# Removes session after running and mount the local image repository so that the trivy docker can scan
# Additionally, set a higher timeout and enable vuln for faster execution
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image --timeout 15m --scanners vuln --severity HIGH,CRITICAL kaichua95/fastapi-app
```

### Results
```
Python (python-pkg)
===================
Total: 6 (HIGH: 6, CRITICAL: 0)

┌──────────────────┬───────────────┬──────────┬────────┬───────────────────┬───────────────┬──────────────────────────────────────────────────────────────┐
│     Library      │ Vulnerability │ Severity │ Status │ Installed Version │ Fixed Version │                            Title                             │
├──────────────────┼───────────────┼──────────┼────────┼───────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ keras (METADATA) │ CVE-2025-8747 │ HIGH     │ fixed  │ 3.10.0            │ 3.11.0        │ CVE-2025-8747 affecting package keras for versions less than │
│                  │               │          │        │                   │               │ 3.3.3-3                                                      │
│                  │               │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2025-8747                    │
│                  │               │          │        │                   │               │                                                              │
│                  │               │          │        │                   │               │                                                              │
│                  │               │          │        │                   │               │                                                              │
│                  │               │          │        │                   │               │                                                              │
│                  ├───────────────┤          │        │                   ├───────────────┼──────────────────────────────────────────────────────────────┤
│                  │ CVE-2025-9905 │          │        │                   │ 3.11.3        │ keras: Arbitary Code execution in Keras load_model()         │
│                  │               │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2025-9905                    │
│                  │               │          │        │                   │               │                                                              │
│                  │               │          │        │                   │               │                                                              │
│                  │               │          │        │                   │               │                                                              │
│                  ├───────────────┤          │        │                   ├───────────────┼──────────────────────────────────────────────────────────────┤
│                  │ CVE-2025-9906 │          │        │                   │ 3.11.0        │ keras: Arbitrary Code execution in Keras Safe Mode           │
│                  │               │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-20│                  │               │          │        │                   │               │                                                              │
│                  │               │          │        │                   │               │                                   │                  │               │          │        │                   │               │                                                              │
│                  ├───────────────┤          │        │                   ├───────────────┼──────────────────────────────────────────────────────────────┤
│                  │ CVE-2025-9906 │          │        │                   │ 3.11.0        │ keras: Arbitrary Code execution in Keras Safe Mode           │
│                  │               │          │        │                   │               │ https://avd.aquasec.com/nvd/cve-2025-9906                    │
│                  │               │          │        │                   │               │                                                              │
│                  │               │          │        │                   │               │                                                              │
│                  │               │          │        │                   │               │                                                              │
└──────────────────┴───────────────┴──────────┴────────┴───────────────────┴───────────────┴──────────────────────────────────────────────────────────────┘
```

## Open Source Tools for kubernetes (Local Service)
### kubectl
#### Installing (Windows)
1. Download the exe from https://dl.k8s.io/v1.34.1/bin/windows/amd64/kubectl.exe
2. Extract the zip file into ``"C:\Program Files\kubectl\"``.
3. In environment variables, add the ``"C:\Program Files\kubectl\"`` to the path variable. 
4. Run ``kubectl version --client`` to verify if working as intended.

**Note: If you installed "Docker for Windows", ``kubectl`` is installed under ``"C:\Program Files\Docker\Docker\resources\bin"``.*

### minikube
#### Installing (Windows)
Open Powershell with **administrator** and run:
```
 New-Item -Path 'c:\' -Name 'minikube' -ItemType Directory -Force

 $ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -OutFile 'c:\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/latest/download/minikube-windows-amd64.exe' -UseBasicParsing
```
The ``minikube`` can be found in "C:\minikube".

Proceed to add the executable into the path variable. 
```
$oldPath = [Environment]::GetEnvironmentVariable('Path', [EnvironmentVariableTarget]::Machine)
if ($oldPath.Split(';') -inotcontains 'C:\minikube'){
  [Environment]::SetEnvironmentVariable('Path', $('{0};C:\minikube' -f $oldPath), [EnvironmentVariableTarget]::Machine)
}
```
To test that minikube is properly configured and install, open cmd with **administrator** and run ``minikube start``.

There will be a message *"Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default"* which signifies that ``minikube`` can be managed using ``kubectl``.

## Digital Ocean for kubernetes (Cloud Service - Chargable)
### Installing (Windows)
To install ``doctl`` from digital ocean, open Powershell with **administrator** and run:
```
Invoke-WebRequest https://github.com/digitalocean/doctl/releases/download/v1.145.0/doctl-1.145.0-windows-amd64.zip -OutFile ~\doctl-1.145.0-windows-amd64.zip
```
The zip file should be downloaded to your home folder located at "C:\Users\Wen Kai Chua\" for example.

Once the zip file has been downloaded, perform the following steps:
```
New-Item -ItemType Directory $env:ProgramFiles\doctl\

Expand-Archive -Path ~/doctl-1.145.0-windows-amd64.zip -DestinationPath $env:ProgramFiles\doctl\

[Environment]::SetEnvironmentVariable(
    "Path",
    [Environment]::GetEnvironmentVariable("Path",
    [EnvironmentVariableTarget]::Machine) + ";$env:ProgramFiles\doctl\",
    [EnvironmentVariableTarget]::Machine)

$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
```

### Initializing Kubernetes Cluster
Need to create an access token via https://cloud.digitalocean.com/account/api/tokens and enter the token using ``doctl auth init``.

Run the next command to spin up kubernetes instances.
```
doctl kubernetes cluster create my-cluster --region nyc1 --version 1.33.1-do.2 --node-pool "name=default;size=s-2vcpu-4gb;count=2"

doctl kubernetes cluster kubeconfig save my-cluster

kubectl get nodes
```


## Kubernetes
### Deployment & Verification (Using Open Source Tools)
#### Deployment (using kubectl)
To apply the deployment and service yaml files to the Kubernetes, open cmd with **administrator** and the following:
```
# <Directory> is the folder holding the deployment and service files 
# (e.g. "C:\Users\Wen Kai Chua\Desktop\NUS ISS\LLMSecOps-Workshop\")

cd <Directory>

minikube start
kubectl apply –f deployment.yaml –f service.yaml
```
Next, confirm that the deployment has been registered. 
1. By running ``kubectl get deploy``, the output is:
```
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
gpt-huggingface   0/3     3            0           37s
```

2. By running ``kubectl get services``, the output is:
```
NAME             TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
gpt-hf-service   NodePort    10.107.96.25   <none>        4000:30007/TCP   16s
kubernetes       ClusterIP   10.96.0.1      <none>        443/TCP          8m35s
```

#### Verification (using kube-score)
Kube-score is a tool that is able to perform static code analysis of Kubernetes object definitions. 

For Windows, it can be accessed via https://kube-score.com/ and paste the definitions into the text box OR perform the analysis via docker.

##### Docker Example
1. Pull the image from Docker hub via ``docker pull zegl/kube-score:latest``.
2. Next, change directory to the folder holding the .yaml files.
3. Run ``docker run -i zegl/kube-score:latest score - --output-format ci < deployment.yaml`` for example to analyse the "deployment.yaml".

Given the following, 
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: gpt-huggingface
spec:
  replicas: 3
  selector:
    matchLabels:
      app: gpt-hf-prod
  template:
    metadata:
      labels:
        app: gpt-hf-prod
    spec:
      containers:
      - name: gptcontainer
        image: kaichua95/fastapi-app'
        ports:
        - containerPort: 4000
---
apiVersion: v1
kind: Service
metadata:
  name: gpt-hf-service
spec:
  type: NodePort
  selector:
    app: gpt-hf-pod
  ports:
  - port: 4000
    targetPort: 4000
    nodePort: 30007
```
The output is as follows:
```
apps/v1/Deployment gpt-huggingface 💥
    path=input
    [CRITICAL] Container Security Context ReadOnlyRootFilesystem
        · gptcontainer -> Container has no configured security context
            Set securityContext to run the container in a more secure context.
    [CRITICAL] Container Resources
        · gptcontainer -> CPU limit is not set
            Resource limits are recommended to avoid resource DDOS. Set
            resources.limits.cpu
        · gptcontainer -> Memory limit is not set
            Resource limits are recommended to avoid resource DDOS. Set
            resources.limits.memory
        · gptcontainer -> CPU request is not set
            Resource requests are recommended to make sure that the application
            can start and run without crashing. Set resources.requests.cpu
        · gptcontainer -> Memory request is not set
            Resource requests are recommended to make sure that the application
            can start and run without crashing. Set resources.requests.memory
    [CRITICAL] Container Ephemeral Storage Request and Limit
        · gptcontainer -> Ephemeral Storage limit is not set
            Resource limits are recommended to avoid resource DDOS. Set
            resources.limits.ephemeral-storage
        · gptcontainer -> Ephemeral Storage request is not set
            Resource requests are recommended to make sure the application can
            start and run without crashing. Set
            resource.requests.ephemeral-storage
    [CRITICAL] Pod NetworkPolicy
        · The pod does not have a matching NetworkPolicy
            Create a NetworkPolicy that targets this pod to control who/what
            can communicate with this pod. Note, this feature needs to be
            supported by the CNI implementation used in the Kubernetes cluster
            to have an effect.
    [CRITICAL] Container Image Tag
        · gptcontainer -> Image with latest tag
            Using a fixed tag is recommended to avoid accidental upgrades
    [CRITICAL] Container Security Context User Group ID
        · gptcontainer -> Container has no configured security context
            Set securityContext to run the container in a more secure context.
    [CRITICAL] Deployment has PodDisruptionBudget
        · No matching PodDisruptionBudget was found
            It's recommended to define a PodDisruptionBudget to avoid
            unexpected downtime during Kubernetes maintenance operations, such
            as when draining a node.
    [WARNING] Deployment has host PodAntiAffinity
        · Deployment does not have a host podAntiAffinity set
            It's recommended to set a podAntiAffinity that stops multiple pods
            from a deployment from being scheduled on the same node. This
            increases availability in case the node becomes unavailable.
v1/Service gpt-hf-service 💥
    path=input
    [WARNING] Service Type
        · The service is of type NodePort
            NodePort services should be avoided as they are insecure, and can
            not be used together with NetworkPolicies. LoadBalancers or use of
            an Ingress is recommended over NodePorts.
    [CRITICAL] Service Targets Pod
        · The services selector does not match any pods
```

## Helm
### Installing (Windows)
1. Download the zip file from ``https://get.helm.sh/helm-v3.19.0-windows-amd64.zip``
2. Extract the zip file into ``"C:\Program Files\helm\"``.
3. In environment variables, add the ``"C:\Program Files\helm\"`` to the path variable.
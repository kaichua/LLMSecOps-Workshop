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
docker build -t <your-name>/fastapi-app .
```

### Running Docker Image
To run the docker image after it has been built execute the following:
```
docker run -p 4000:4000 <your-name>/fastapi-app
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
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image --timeout 15m --scanners vuln --severity HIGH,CRITICAL wenkai/fastapi-app
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

## Digital Ocean
### Installing (Windows)
To install ``doctl`` from digital ocean, open Powershell with administrator and run:
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

## Helm
### Installing (Windows)
1. Download the zip file from ``https://get.helm.sh/helm-v3.19.0-windows-amd64.zip``
2. Extract the zip file into ``"C:\Program Files\helm\"``.
3. In environment variables, add the ``"C:\Program Files\helm\"`` to the path variable.
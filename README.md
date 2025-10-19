# LLMSecOps-Workshop

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
Open Powershell and run the following:
```
Invoke-WebRequest https://github.com/digitalocean/doctl/releases/download/v1.141.0/doctl-1.141.0-windows-amd64.zip -OutFile ~\doctl-1.141.0-windows-amd64.zip
```
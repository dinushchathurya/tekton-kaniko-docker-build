### Tekton GitHub Trigger

### Task List

- [x] Clone Private Git Repository
- [x] Build Docker Image
- [x] Push Docker Image to Private Docker Registry
- [x] Update ArgoCD manifest with new image tag and write-back to manifest repo
- [x] Configure Tekton Triggers

### Branches

- [x] master (Basic Pipeline with Git Clone, Build and Push) 
- [x] argo-manifest-update (Update ArgoCD manifest with new image tag and write-back to manifest repo)
- [x] setup-triggers (Configure Tekton Triggers to trigger pipeline on push to master branch)
### Install Tekton Tasks

```
tkn hub install task git-clone

tkn hub install task kaniko
```

### Commands

```
kubectl create -f tekton/pipeline/pipeline.yaml

kubectl create -f tekton/pipelineRun/pipelineRun.yaml
```

### GitHub Authentication

#### Genearte SSH Key

```
ssh-keygen -t rsa -b 4096 -C "tekton@tekton.dev"
```

###E Encode SSH Key

```
cat ~/.ssh/tekton_rsa | base64 -w 0
```

#### Create Secret

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: git-ssh-key
  annotations:
    tekton.dev/git-0: github.com
type: kubernetes.io/ssh-auth
data:
  ssh-privatekey: <base64 encoded private key>
```

#### Create Service Account

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: git-service-account
secrets:
  - name: git-ssh-key ### Secret name
```

#### Add Public Key to GitHub

```
cat ~/.ssh/tekton_rsa.pub
``` 

Now go to GitHub -> Settings -> SSH and GPG keys -> New SSH Key and paste the public key.

### DockerHub Authentication

#### Create Docker Registry Config

```bash
mkdir -p ~/.docker && echo '{"auths": {"https://index.docker.io/v1/": {"username": "", "password": "", "email": ""}}}' > ~/.docker/config.json
```

#### Create Base64 Encoded Docker Registry Secret

```bash
cat ~/.docker/config.json | base64 -w0
``` 

#### Create Docker Registry Secret

```yaml

apiVersion: v1
kind: Secret
metadata:
  name: docker-credential
data:
  config.json: <base64 encoded docker config>
```





  

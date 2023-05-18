### Tekton GitHub Trigger

### Install Tekton Tasks

```
tkn hub install task git-clone

tkn hub install task kaniko
```

### Commands

```
kubectl create -f tekton/tasks/list-source.yaml ### Custom task to list source files

kubectl create -f tekton/tasks/update-manifest.yaml ### Custom task to update ArgoCD manifest

kubectl create -f tekton/pipeline/pipeline.yaml ### Pipeline definition

kubectl create -f tekton/pipelineRun/pipelineRun.yaml ### PipelineRun definition
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





  

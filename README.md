Gitalab CI Pipeline

Manifest Repo

ArgoCD 


1- Developers push in repositories 
	1.1 Install ArgoCD
 
2- Configure .gitlab-ci.yml 

3- GitaLab CI Pipeline (Run) 

4- push In Docker Hub and Update the Helm

5- ArgoCD catch Helm changes

6- Publish in Kubernetes

1.1 ArgoCD installation on Kubernetes
https://argo-cd.readthedocs.io/en/stable/getting_started/

a) Install Argo CD

 Commands to install
	kubectl create namespace argocd
	kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

Commands to show 

```bash
kubectl -n argocd get all
```
Result:


```bash
sudo kubectl -n argocd get all
NAME                                                   READY   STATUS    RESTARTS   AGE
pod/argocd-application-controller-0                    1/1     Running   0          42s
pod/argocd-applicationset-controller-764744455-97rj7   1/1     Running   0          42s
pod/argocd-dex-server-78df54498c-jlcz5                 1/1     Running   0          42s
pod/argocd-notifications-controller-577b87f4bd-jts6n   1/1     Running   0          42s
pod/argocd-redis-564c8b4dd7-rsnl2                      1/1     Running   0          42s
pod/argocd-repo-server-6858df88f4-vwknq                1/1     Running   0          42s
pod/argocd-server-74b5b78785-mjh87                     1/1     Running   0          42s

NAME                                              TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
service/argocd-applicationset-controller          ClusterIP   10.96.226.43   <none>        7000/TCP,8080/TCP            42s
service/argocd-dex-server                         ClusterIP   10.96.247.23   <none>        5556/TCP,5557/TCP,5558/TCP   42s
service/argocd-metrics                            ClusterIP   10.96.37.14    <none>        8082/TCP                     42s
service/argocd-notifications-controller-metrics   ClusterIP   10.96.64.133   <none>        9001/TCP                     42s
service/argocd-redis                              ClusterIP   10.96.173.43   <none>        6379/TCP                     42s
service/argocd-repo-server                        ClusterIP   10.96.25.17    <none>        8081/TCP,8084/TCP            42s
service/argocd-server                             ClusterIP   10.96.202.16   <none>        80/TCP,443/TCP               42s
service/argocd-server-metrics                     ClusterIP   10.96.247.7    <none>        8083/TCP                     42s

NAME                                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/argocd-applicationset-controller   1/1     1            1           42s
deployment.apps/argocd-dex-server                  1/1     1            1           42s
deployment.apps/argocd-notifications-controller    1/1     1            1           42s
deployment.apps/argocd-redis                       1/1     1            1           42s
deployment.apps/argocd-repo-server                 1/1     1            1           42s
deployment.apps/argocd-server                      1/1     1            1           42s

NAME                                                         DESIRED   CURRENT   READY   AGE
replicaset.apps/argocd-applicationset-controller-764744455   1         1         1       42s
replicaset.apps/argocd-dex-server-78df54498c                 1         1         1       42s
replicaset.apps/argocd-notifications-controller-577b87f4bd   1         1         1       42s
replicaset.apps/argocd-redis-564c8b4dd7                      1         1         1       42s
replicaset.apps/argocd-repo-server-6858df88f4                1         1         1       42s
replicaset.apps/argocd-server-74b5b78785                     1         1         1       42s

NAME                                             READY   AGE
statefulset.apps/argocd-application-controller   1/1     42s

```


Configure Access ArgoCD external:
 
service/argocd-server  ClusterIP 

We need to change the service/argocd-server type "ClusterIP" to "NodePort"

Commands open config


```bash
kubectl -n argocd edit service 
```
	
	
Go to 
	in spec > type : NodePort
	
See this ok

Commands to show 
```bash
kubectl -n argocd get all
```

```bash
andre@andre-ThinkCentre-M70q:~/Documents/CreateCluster$ sudo kubectl -n argocd get all
NAME                                                   READY   STATUS    RESTARTS   AGE
pod/argocd-application-controller-0                    1/1     Running   0          16m
pod/argocd-applicationset-controller-764744455-97rj7   1/1     Running   0          16m
pod/argocd-dex-server-78df54498c-jlcz5                 1/1     Running   0          16m
pod/argocd-notifications-controller-577b87f4bd-jts6n   1/1     Running   0          16m
pod/argocd-redis-564c8b4dd7-rsnl2                      1/1     Running   0          16m
pod/argocd-repo-server-6858df88f4-vwknq                1/1     Running   0          16m
pod/argocd-server-74b5b78785-mjh87                     1/1     Running   0          16m

NAME                                              TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
service/argocd-applicationset-controller          ClusterIP   10.96.226.43   <none>        7000/TCP,8080/TCP            16m
service/argocd-dex-server                         ClusterIP   10.96.247.23   <none>        5556/TCP,5557/TCP,5558/TCP   16m
service/argocd-metrics                            ClusterIP   10.96.37.14    <none>        8082/TCP                     16m
service/argocd-notifications-controller-metrics   ClusterIP   10.96.64.133   <none>        9001/TCP                     16m
service/argocd-redis                              ClusterIP   10.96.173.43   <none>        6379/TCP                     16m
service/argocd-repo-server                        ClusterIP   10.96.25.17    <none>        8081/TCP,8084/TCP            16m
service/argocd-server                             NodePort    10.96.202.16   <none>        80:31392/TCP,443:30751/TCP   16m
service/argocd-server-metrics                     ClusterIP   10.96.247.7    <none>        8083/TCP                     16m

NAME                                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/argocd-applicationset-controller   1/1     1            1           16m
deployment.apps/argocd-dex-server                  1/1     1            1           16m
deployment.apps/argocd-notifications-controller    1/1     1            1           16m
deployment.apps/argocd-redis                       1/1     1            1           16m
deployment.apps/argocd-repo-server                 1/1     1            1           16m
deployment.apps/argocd-server                      1/1     1            1           16m

NAME                                                         DESIRED   CURRENT   READY   AGE
replicaset.apps/argocd-applicationset-controller-764744455   1         1         1       16m
replicaset.apps/argocd-dex-server-78df54498c                 1         1         1       16m
replicaset.apps/argocd-notifications-controller-577b87f4bd   1         1         1       16m
replicaset.apps/argocd-redis-564c8b4dd7                      1         1         1       16m
replicaset.apps/argocd-repo-server-6858df88f4                1         1         1       16m
replicaset.apps/argocd-server-74b5b78785                     1         1         1       16m

NAME                                             READY   AGE
statefulset.apps/argocd-application-controller   1/1     16m
```

 
Find the IP address of kubernetes Node.

Commands Find the IP

```bash
kubectl get node -o wide
```

```bash
andre@andre-ThinkCentre-M70q:~/Documents/CreateCluster$ sudo kubectl get node -o wide
NAME                 STATUS   ROLES           AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION     CONTAINER-RUNTIME
kind-control-plane   Ready    control-plane   26m   v1.27.3   172.18.0.2    <none>        Debian GNU/Linux 11 (bullseye)   6.8.0-51-generic   containerd://1.7.1
```

The Ip is INTERNAL-IP 

In Browser http://<INTERNAL-IP>:<NodePort>

http://172.18.0.2:31392

You'll see the login page.


b) Download Argo CD CLI

Go to googlr and search : install argocd cli in linux

https://argo-cd.readthedocs.io/en/stable/cli_installation/

Login Using The CLI

https://argo-cd.readthedocs.io/en/stable/getting_started/

Command 

```bash
argocd admin initial-password -n argocd
```

this will generate the password : <PASSWORD>

```bash
andre@andre-ThinkCentre-M70q:~/Documents/CreateCluster$ sudo argocd admin initial-password -n argocd
<PASSWORD>

 This password must be only used for first time login. We strongly recommend you update the password using `argocd account update-password`.
```
Go to the Login page : http://172.18.0.2:31392


user : admin

password : <PASSWORD>


2 Configure .gitlab-ci.yml

`"**UNDERSTAND WHAT WE NEED**"`

![](values-yaml-path-image.png)

We need pull this image `login_demo:38d87451` from this location `registry.gitlab.com/devops/code-repo/` to deploy on kubernets cluster.

So by CICD ate first 

a) Create this image `login_demo:38d87451`

b) Publish this `registry.gitlab.com/devops/code-repo/` repository.

c) Then this chart can pull it and Deploy on Cluster.

Search in google : How to push image on gitlab

https://docs.gitlab.com/ee/user/packages/container_registry/build_and_push_images.html

Let's create a new file .gitlab-ci.yml  in the Code Repo.

Example

```yml
build:
  image: docker:20.10.16
  stage: build
  services:
    - docker:20.10.16-dind
  variables:
    IMAGE_TAG: $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG
  script:
    - echo "$CI_REGISTRY_PASSWORD" | docker login $CI_REGISTRY -u $CI_REGISTRY_USER --password-stdin
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG

```

in .gitlab-ci.yml file

```yml
stages:
  - build

build_image:
  image: docker
  stage: build
  services:
    - docker:dind
  variables:
    IMAGE_TAG: $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG
  script:
    - echo "$CI_REGISTRY_PASSWORD" | docker login $CI_REGISTRY -u $CI_REGISTRY_USER --password-stdin
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG

```

https://docs.gitlab.com/ee/ci/variables/

To fill the $IMAGE_TAG we need go to the repository.

Left menu go to Deploy > Container Registry 

Here you see the full command 

Copy the secund command [#]


```yml
stages:
  - build
  

variables:
  APP_NAME: login_demo
  IMAGE_TAG: registry.gitlab.com/dev-ops-group3/code-repo/$APP_NAME:$CI_COMMIT_SHORT_SHA
  

build_image:
  image: docker
  stage: build
  services:
    - docker:dind
  variables:
    IMAGE_TAG: $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG
  script:
    - echo "$CI_REGISTRY_PASSWORD" | docker login $CI_REGISTRY -u $CI_REGISTRY_USER --password-stdin
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG

```

3- GitaLab CI Pipeline (Run)

In this step we need to update Manifest Helm Chart

For this é have to difine a new stage in .gitlab-ci.yml file

Here we'll add a new stage update_helm_chart

For this stage we have to define a new job

```yml
stages:
  - build
  - update_helm_chart

variables:
  APP_NAME: login_demo
  IMAGE_TAG: registry.gitlab.com/dev-ops-group3/code-repo/$APP_NAME:$CI_COMMIT_SHORT_SHA
  

build_image:
  image: docker
  stage: build
  services:
    - docker:dind
  variables:
    IMAGE_TAG: $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG
  script:
    - echo "$CI_REGISTRY_PASSWORD" | docker login $CI_REGISTRY -u $CI_REGISTRY_USER --password-stdin
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG

update_helm_chart:
  stage: update_helm_chart
```

Let explain :

CI bring up a Docker machine on Gitlab Runner

Docker machine on Gitlab Runner should go and clone the Manifest Repo 
```bash 
	mkdir test_demo
	cd test_demo
```
here the Docker machine will clone the repo on a paste test_demo

```bash 
	git clone <MANIFEST_REPO>
```

The first challenge : needed the credentials and we can't not hard code the password in CI/CD pipeline

To solve this problema we'll 

a) Create a ssh key.

b) Difine a variable in project 

c) Use in CI/CD pipeline

https://docs.gitlab.com/ee/user/ssh.html#generate-an-ssh-key-pair

Create a ssh-key

```bash
ssh-keygen -t rsa -b 4096 -C "devops@example.com"
```

```bash
PS C:\Users\andre\.ssh> cat id_rsa.pub
ssh-rsa AAAAB3NzaC
PS C:\Users\andre\.ssh>
```

Add SSH Key Public to account user in GitaLab Server

	* select your profile > edit profile > SSH Keys > Add New Key paste the value, put title.
	
Refer SSH Key Private in the Code repo: 

	* on a left menu > Settings > CI/CD > Variables > (CI/CD Variables) > Add Variable > SSH_PRIVATE_KEY

Now we need to automate this process.

Back to the .gitlab-ci.yml file

Add a image in the stage update_helm_chart


```yml
stages:
  - build
  - update_helm_chart

variables:
  APP_NAME: login_demo
  IMAGE_TAG: registry.gitlab.com/dev-ops-group3/code-repo/$APP_NAME:$CI_COMMIT_SHORT_SHA
  

build_image:
  image: docker
  stage: build
  services:
    - docker:dind
  variables:
    IMAGE_TAG: $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG
  script:
    - echo "$CI_REGISTRY_PASSWORD" | docker login $CI_REGISTRY -u $CI_REGISTRY_USER --password-stdin
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG

update_helm_chart:
  stage: update_helm_chart
  image: ubuntu:22.04
before_script:
  # which ssh-agent: Checks if the ssh-agent command is available. 
  # If not, it updates the package list (apt-get update -y) and installs the required tools 
  # ssh-agent is required to manage SSH keys for authentication, and git is needed to interact with Git repositories.
  - 'which ssh-agent || ( apt-get update -y && apt-get install openssh-client git -y )'
  - mkdir -p /root/.ssh
  - echo "$SSH_PRIVATE_KEY" > /root/.ssh/id_rsa
  # Set Correct Permissions on the Private Key. 
  # Changes the file permissions of the private key to 600, which allows only the owner to read/write the file.
  - chmod 600 /root/.ssh/id_rsa
  # Retrieves the SSH public key of gitlab.com and appends it to the ~/.ssh/known_hosts file.
  # Ensures that the host key of gitlab.com is trusted, avoiding prompts during SSH connections.
  - ssh-keyscan -H gitlab.com >> ~/.ssh/known_hosts
  # Changes the file permissions of ~/.ssh/known_hosts to 644, making it readable by others but writable only by the owner.
  # Proper permissions are required for the SSH client to use the known_hosts file securely.
  - chmod 644 ~/.ssh/known_hosts
  # run ssh-agent
  - eval $(ssh-agent -s)
  # add ssh key stored in SSH_PRIVATE_KEY variable to the agent store
  # tr -d '\r': Removes carriage return characters (\r) to ensure the key format is 
  # ssh-add -: Adds the private key to the ssh-agent.
  - echo "$SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add -
  # Git
  - git config --global user.email "gitlab-ci@gmail.com"
  - git config --global user.name "gitlab-ci"
  - git clone git@gitlab.com:d6245/manifest-repo.git
  - cd manifest-repo
  # Lists all files in the manifest-repo directory with details such as permissions, size, and modification time.
  - ls -latr
script:
# Update Image TAG
  - sed -i "s/login_demo:.*/login_demo:${CI_COMMIT_SHORT_SHA}/g" test-login-app/values.yaml
  - git add test-login-app/values.yaml
  - git commit -am "Update Image"
  - git push
```

4- push In Docker Hub and Update the Helm

In the Manifest Repo we have this structure

Manifest-Repo
|_test-login-app
	|_deployment.yaml
	|_secret.yaml
	|_service.yaml
|_Chart.yaml
|_values.yaml


1- Manifest-Repo (Root)
    The top-level folder, Manifest-Repo, represents the Helm chart repository that contains all the necessary files for deploying the Kubernetes application.

2- Primary Branches
        test-login-app: This subfolder holds Kubernetes manifest files for specific resources (e.g., deployments, services, secrets).
	
        Chart.yaml: Defines metadata about the Helm chart, such as its name, version, and application version.
	
        values.yaml: Contains configurable values that the Helm chart templates will use, like image repository, replica count, and service type.

3- Sub-Branches under test-login-app
        deployment.yaml: Specifies the deployment details for the application, such as replicas, image, labels, and imagePullSecrets. It also references variables from values.yaml (e.g., .Values.image.repository and .Release.Name).
	
        secret.yaml: Defines a Kubernetes secret (e.g., helm-secret) for storing credentials needed for pulling images from private repositories.
	
        service.yaml: Configures a Kubernetes service to expose the application, such as NodePort, LoadBalancer, or ClusterIP. It uses variables from values.yaml, like the service type.

4- Connections Between Files
        values.yaml: Supplies configuration values to the templates (deployment.yaml and service.yaml). For instance:
	
            The container image repository in deployment.yaml is fetched from values.yaml.
	    
            The service type (NodePort, ClusterIP) in service.yaml is also defined in values.yaml.
	    
        Chart.yaml: Acts as metadata for the Helm chart but does not directly link to specific files.

This flow shows how the values.yaml file centralizes configurations while the other YAML files focus on defining Kubernetes resources.


Go to values.yaml file in Manifest Repo. Here we'll edit the Image repository version

Fist go the Dev-Ops-Group > code-repo > Container Registry > code-repo

Copy image tag and put in propity `repository`

`repository: registry.gitlab.com/dev-ops-group3/code-repo/login_demo:main` 

```yaml
# Default values for test-login-app.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: registry.gitlab.com/dev-ops-group3/code-repo/login_demo:main
  pullPolicy: IfNotPresent
  # Overrides the image tag whose default is the chart appVersion.
  tag: ""

imagePullSecrets: []
nameOverride: ""
fullnameOverride: ""

serviceAccount:
  # Specifies whether a service account should be created
  create: true
  # Annotations to add to the service account
  annotations: {}
  # The name of the service account to use.
  # If not set and create is true, a name is generated using the fullname template
  name: ""

podAnnotations: {}

podSecurityContext: {}
  # fsGroup: 2000

securityContext: {}
  # capabilities:
  #   drop:
  #   - ALL
  # readOnlyRootFilesystem: true
  # runAsNonRoot: true
  # runAsUser: 1000

service:
  type: NodePort
  port: 80

ingress:
  enabled: false
  className: ""
  annotations: {}
    # kubernetes.io/ingress.class: nginx
    # kubernetes.io/tls-acme: "true"
  hosts:
    - host: chart-example.local
      paths:
        - path: /
          pathType: ImplementationSpecific
  tls: []
  #  - secretName: chart-example-tls
  #    hosts:
  #      - chart-example.local

resources: {}
  # We usually recommend not to specify default resources and to leave this as a conscious
  # choice for the user. This also increases chances charts run on environments with little
  # resources, such as Minikube. If you do want to specify resources, uncomment the following
  # lines, adjust them as necessary, and remove the curly braces after 'resources:'.
  # limits:
  #   cpu: 100m
  #   memory: 128Mi
  # requests:
  #   cpu: 100m
  #   memory: 128Mi

autoscaling:
  enabled: false
  minReplicas: 1
  maxReplicas: 100
  targetCPUUtilizationPercentage: 80
  # targetMemoryUtilizationPercentage: 80

nodeSelector: {}

tolerations: []

affinity: {}


```

5- ArgoCD catch Helm changes

Open ArgoCD 

Define Manifest Repo
Go to Setting > Repositories > Connect Repo > 

	Choose your connection method > https
 
	Type > git
 
	Project> default
 
	Repository URL > url git repo Manifest Repo
	
	click in connection
	
Build apllication

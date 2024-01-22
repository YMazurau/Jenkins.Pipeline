Started by user ym
Obtained Jenkinsfile from git https://github.com/YMazurau/Jenkins.Pipeline.git
[Pipeline] Start of Pipeline
[Pipeline] node
Running on node115 in /var/lib/jenkins/workspace/ITBootcampTask
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Declarative: Checkout SCM)
[Pipeline] checkout
Selected Git installation does not exist. Using Default
The recommended git tool is: NONE
No credentials specified
Fetching changes from the remote Git repository
Checking out Revision 4a4de53123f0b2fbe4758db97a3eb96f0d29fa51 (refs/remotes/origin/master)
Commit message: "preprod"
 > git rev-parse --resolve-git-dir /var/lib/jenkins/workspace/ITBootcampTask/.git # timeout=10
 > git config remote.origin.url https://github.com/YMazurau/Jenkins.Pipeline.git # timeout=10
Fetching upstream changes from https://github.com/YMazurau/Jenkins.Pipeline.git
 > git --version # timeout=10
 > git --version # 'git version 2.34.1'
 > git fetch --tags --force --progress -- https://github.com/YMazurau/Jenkins.Pipeline.git +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git rev-parse refs/remotes/origin/master^{commit} # timeout=10
 > git config core.sparsecheckout # timeout=10
 > git checkout -f 4a4de53123f0b2fbe4758db97a3eb96f0d29fa51 # timeout=10
 > git rev-list --no-walk 6b1074fd6aa9007e7f1846fdd729e7491989db5f # timeout=10
[Pipeline] }
[Pipeline] // stage
[Pipeline] withEnv
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Clone Git repository)
[Pipeline] git
Selected Git installation does not exist. Using Default
The recommended git tool is: NONE
No credentials specified
Fetching changes from the remote Git repository
Checking out Revision 4a4de53123f0b2fbe4758db97a3eb96f0d29fa51 (refs/remotes/origin/master)
Commit message: "preprod"
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Build and Test Image)
[Pipeline] sh
 > git rev-parse --resolve-git-dir /var/lib/jenkins/workspace/ITBootcampTask/.git # timeout=10
 > git config remote.origin.url https://github.com/YMazurau/Jenkins.Pipeline.git # timeout=10
Fetching upstream changes from https://github.com/YMazurau/Jenkins.Pipeline.git
 > git --version # timeout=10
 > git --version # 'git version 2.34.1'
 > git fetch --tags --force --progress -- https://github.com/YMazurau/Jenkins.Pipeline.git +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git rev-parse refs/remotes/origin/master^{commit} # timeout=10
 > git config core.sparsecheckout # timeout=10
 > git checkout -f 4a4de53123f0b2fbe4758db97a3eb96f0d29fa51 # timeout=10
 > git branch -a -v --no-abbrev # timeout=10
 > git branch -D master # timeout=10
 > git checkout -b master 4a4de53123f0b2fbe4758db97a3eb96f0d29fa51 # timeout=10
+ sleep 5
[Pipeline] sh
+ docker-compose up -d
 Network itbootcamptask_default  Creating
 Network itbootcamptask_default  Created
 Container itbootcamptask-postgres-1  Creating
 Container itbootcamptask-postgres-1  Created
 Container itbootcamptask-nodejs-1  Creating
 Container itbootcamptask-nodejs-1  Created
 Container itbootcamptask-postgres-1  Starting
 Container itbootcamptask-postgres-1  Started
 Container itbootcamptask-nodejs-1  Starting
 Container itbootcamptask-nodejs-1  Started
[Pipeline] timeout
Timeout set to expire in 5 sec
[Pipeline] {
[Pipeline] retry
[Pipeline] {
[Pipeline] script
[Pipeline] {
[Pipeline] sh
+ curl -s -o /dev/null -w %{http_code}
 http://localhost:3000
+ response=200
+ echo 200
200
+ [ 200 != 200 ]
[Pipeline] }
[Pipeline] // script
[Pipeline] }
[Pipeline] // retry
[Pipeline] }
[Pipeline] // timeout
[Pipeline] sh
+ docker build -t ymazurau/project:itbtv.1 .
#0 building with "default" instance using docker driver

#1 [internal] load build definition from Dockerfile
#1 transferring dockerfile: 594B done
#1 DONE 0.0s

#2 [auth] library/node:pull token for registry-1.docker.io
#2 DONE 0.0s

#3 [internal] load metadata for docker.io/library/node:14
#3 DONE 1.0s

#4 [internal] load .dockerignore
#4 transferring context: 2B done
#4 DONE 0.0s

#5 [1/5] FROM docker.io/library/node:14@sha256:a158d3b9b4e3fa813fa6c8c590b8f0a860e015ad4e59bbce5744d2f6fd8461aa
#5 DONE 0.0s

#6 [internal] load build context
#6 transferring context: 272B done
#6 DONE 0.0s

#7 [4/5] RUN npm install
#7 CACHED

#8 [2/5] WORKDIR /app
#8 CACHED

#9 [3/5] COPY package.json package-lock.json ./
#9 CACHED

#10 [5/5] COPY ./src .
#10 CACHED

#11 exporting to image
#11 exporting layers done
#11 writing image sha256:1b09956db28fd66c467603e98ad91175268581fc98be987195e89fe5a3b4ea7a 0.0s done
#11 naming to docker.io/ymazurau/project:itbtv.1 done
#11 DONE 0.0s
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Validate Dockerfile)
[Pipeline] sh
+ docker run --rm -i hadolint/hadolint
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Push Image to Registry)
[Pipeline] script
[Pipeline] {
[Pipeline] withCredentials
Masking supported pattern matches of $REGISTRY_PASSWORD
[Pipeline] {
[Pipeline] sh
+ docker login -u ymazurau -p ****
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
WARNING! Your password will be stored unencrypted in /var/lib/jenkins/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
[Pipeline] sh
+ docker push ymazurau/project:itbtv.1
The push refers to repository [docker.io/ymazurau/project]
ecc3ba351661: Preparing
d94d72505c2a: Preparing
cccd791f1789: Preparing
783fd6578fb8: Preparing
0d5f5a015e5d: Preparing
3c777d951de2: Preparing
f8a91dd5fc84: Preparing
cb81227abde5: Preparing
e01a454893a9: Preparing
c45660adde37: Preparing
fe0fb3ab4a0f: Preparing
f1186e5061f2: Preparing
b2dba7477754: Preparing
cb81227abde5: Waiting
e01a454893a9: Waiting
c45660adde37: Waiting
fe0fb3ab4a0f: Waiting
f1186e5061f2: Waiting
b2dba7477754: Waiting
3c777d951de2: Waiting
f8a91dd5fc84: Waiting
783fd6578fb8: Layer already exists
ecc3ba351661: Layer already exists
cccd791f1789: Layer already exists
0d5f5a015e5d: Layer already exists
d94d72505c2a: Layer already exists
3c777d951de2: Layer already exists
e01a454893a9: Layer already exists
cb81227abde5: Layer already exists
f8a91dd5fc84: Layer already exists
c45660adde37: Layer already exists
f1186e5061f2: Layer already exists
fe0fb3ab4a0f: Layer already exists
b2dba7477754: Layer already exists
itbtv.1: digest: sha256:b724d0502e5eff22f88271a90da5ac25782c013d6fb46d7a6649fe3df5d7fd00 size: 3048
[Pipeline] }
[Pipeline] // withCredentials
[Pipeline] }
[Pipeline] // script
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Deploy to Pre-Prod)
[Pipeline] input
Approve Deployment to Pre-Prod
Proceed or Abort
Approved by ym
[Pipeline] sh
+ kubectl create namespace preprod
namespace/preprod created
[Pipeline] sh
+ kubectl apply -f dbpass_secret.yaml
sealedsecret.bitnami.com/db-secret created
[Pipeline] sh
+ kubectl apply -f preprod.yaml
Warning: resource namespaces/preprod is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.
namespace/preprod configured
configmap/db-config created
deployment.apps/project created
ingress.networking.k8s.io/project-ingress created
service/project created
[Pipeline] timeout
Timeout set to expire in 5 min 0 sec
[Pipeline] {
[Pipeline] script
[Pipeline] {
[Pipeline] sh
+ sleep 15
[Pipeline] sh
+ kubectl rollout status deployment/project --namespace preprod
deployment "project" successfully rolled out
[Pipeline] echo
Deployment to Pre-Prod is successful
[Pipeline] input
Approve Deployment to Prod
Proceed or Abort
Approved by ym
[Pipeline] }
[Pipeline] // script
[Pipeline] }
[Pipeline] // timeout
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Cleaning)
[Pipeline] sh
+ kubectl delete deployment project --namespace preprod
deployment.apps "project" deleted
[Pipeline] sh
+ kubectl delete namespace preprod
namespace "preprod" deleted
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Deploy to Prod)
[Pipeline] sh
+ kubectl create namespace prod
namespace/prod created
[Pipeline] sh
+ kubectl apply -f Proddbpass_secret.yaml
sealedsecret.bitnami.com/db-secretp created
[Pipeline] sh
+ kubectl apply -f prod.yaml
Warning: resource namespaces/prod is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.
namespace/prod configured
configmap/db-config created
deployment.apps/project created
ingress.networking.k8s.io/project-ingress created
service/project created
[Pipeline] timeout
Timeout set to expire in 5 min 0 sec
[Pipeline] {
[Pipeline] script
[Pipeline] {
[Pipeline] sh
+ kubectl rollout status deployment/project --namespace prod
Waiting for deployment "project" rollout to finish: 0 of 2 updated replicas are available...
Waiting for deployment "project" rollout to finish: 1 of 2 updated replicas are available...
deployment "project" successfully rolled out
[Pipeline] }
[Pipeline] // script
[Pipeline] }
[Pipeline] // timeout
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Declarative: Post Actions)
[Pipeline] slackSend
Slack Send Pipeline step running, values are - baseUrl: <empty>, teamDomain: mazurov1804@gmail.com, channel: incoming-webhooks, color: #00FF00, botUser: true, tokenCredentialId: slack-token1, notifyCommitters: false, iconEmoji: <empty>, username: <empty>, timestamp: <empty>
[Pipeline] sh
+ docker-compose down
 Container itbootcamptask-nodejs-1  Stopping
 Container itbootcamptask-nodejs-1  Stopped
 Container itbootcamptask-nodejs-1  Removing
 Container itbootcamptask-nodejs-1  Removed
 Container itbootcamptask-postgres-1  Stopping
 Container itbootcamptask-postgres-1  Stopped
 Container itbootcamptask-postgres-1  Removing
 Container itbootcamptask-postgres-1  Removed
 Network itbootcamptask_default  Removing
 Network itbootcamptask_default  Removed
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
Finished: SUCCESS
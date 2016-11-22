<!-- .slide: data-background-image="images/the-wheel.jpg" --><!-- https://flic.kr/p/aw67Ea -->
# ![Helm](images/helm.png) <!-- .element: class="plain" -->
## The Package Manager for Kubernetes

---
<!-- .slide: data-background-image="images/schibsted-tech-polska.jpg" --><!-- https://www.facebook.com/SchibstedTechPolska/photos/a.335988559798163.82267.248170668579953/913555652041448/?type=3&theater -->
DevOps Engineer  
[Schibsted Tech Polska](http://www.schibsted.pl/)

[<i class="fa fa-github" aria-hidden="true"></i> @mstrzele](https://github.com/mstrzele)  
[<i class="fa fa-twitter" aria-hidden="true"></i> @mstrzele](https://twitter.com/mstrzele)

---
<!-- .slide: data-background-image="images/mess.jpg" --><!-- https://flic.kr/p/dNPjSx -->
* <!-- .element: class="fragment" --> [`ktmpl`](https://github.com/InQuicker/ktmpl), `kubectl apply -f -` 
* <!-- .element: class="fragment" --> [jenkins-kubernetes-plugin](https://github.com/jenkinsci/kubernetes-plugin)
* <!-- .element: class="fragment" --> [Ansible's `kubernetes`](http://docs.ansible.com/ansible/kubernetes_module.html)
* <!-- .element: class="fragment" --> `kubectl create -f`, `kubectl set deployment image`
* <!-- .element: class="fragment" --> `kubectl create -f`, `kubectl patch`
* <!-- .element: class="fragment" --> `grep`, `awk`, `sed`, `kubectl apply -f -`

#### Notes

* [Setting up Jenkins on Container Engine](https://cloud.google.com/solutions/jenkins-on-container-engine-tutorial)
* [Lab: Build a Continuous Deployment Pipeline with Jenkins and Kubernetes](https://github.com/GoogleCloudPlatform/continuous-deployment-on-kubernetes)
* [Lobsters on Kubernetes](https://github.com/kelseyhightower/lobsters-on-kubernetes)

---
<!-- .slide: data-background-image="images/ships-wheel.jpg" --><!-- https://flic.kr/p/bD8jUV -->
### [Kubernetes Helm](https://github.com/kubernetes/helm)

APT/yum/Homebrew for Kubernetes

#### Notes

* Find and use popular software
* Share your own applications
* Install, update, delete, rollback
* Create reproducible builds
* Manage your Kubernetes manifest files

---
<!-- .slide: data-background-image="images/archives.jpg" --><!-- https://flic.kr/p/6xQ433 -->
![Deis](images/deis.png) <!-- .element: class="plain" -->  
![Google](images/google.png) <!-- .element: class="plain" -->

#### Notes

##### Helm Classic

* Deis v1, CoreOS, fleet
* `deisctl`
* November 2015, [Deis](https://deis.com/)
* Deis v2, Kubernetes
* [Homebrew](http://brew.sh/)


##### Deployment Manager

* [Google Cloud Deployment Manager](https://cloud.google.com/deployment-manager/).

---
<!-- .slide: data-background-image="images/blueprints.jpg" --><!-- https://flic.kr/p/7cxaYh -->

The Helm Client <!-- .element: class="fragment" -->

The Tiller Server <!-- .element: class="fragment" -->

#### Notes

##### The Helm Client

* CLI
* Chart development
* Managing repositories
* Sending charts
* Information about releases
* Upgrading or uninstalling

> is responsible for managing charts

##### The Tiller Server

* `kube-system`
* The Kubernetes API server
* Requests from the Helm client
* Combining a chart and configuration to build a release

> is responsible for managing releases

---
<!-- .slide: data-background-image="images/ship.jpg" --><!-- https://flic.kr/p/yMx2xK -->

Chart <!-- .element: class="fragment" -->

Repository <!-- .element: class="fragment" -->

Release <!-- .element: class="fragment" -->

#### Notes

##### Chart

* Particular directory structure
* Can be packaged into versioned archives

##### Repository

* HTTP server
* `index.yaml`

##### Release

* Instance of a chart

> > The resulting _release_ contains both the build and the config and is ready for immediate execution in the execution environment.

[The Twelve-Factor App](https://12factor.net/build-release-run)

- - -
<!-- .slide: data-background-image="images/ship.jpg" --><!-- https://flic.kr/p/yMx2xK -->
```bash
$ find .
.
./.helmignore
./Chart.yaml
./charts
./charts/mysql-0.2.1.tgz
./requirements.lock
./requirements.yaml
./templates
./templates/_helpers.tpl
./templates/deployment.yaml
./templates/ingress.yaml
./templates/NOTES.txt
./templates/post-install-job.yaml
./templates/secret.yaml
./templates/service.yaml
./values.yaml
```

- - -
<!-- .slide: data-background-image="images/ship.jpg" --><!-- https://flic.kr/p/yMx2xK -->
`./Chart.yaml`

```yaml
name: symfony-demo
version: 0.1.0
description: Symfony Demo Application
keywords:
  - symfony
  - demo
home: http://symfony.com/blog/introducing-the-symfony-demo-application
sources:
  - https://github.com/symfony/symfony-demo
maintainers:
  - name: Maciej Strzelecki
    email: maciej.strzelecki@gmail.com
engine: gotpl
icon: http://symfony.com/apple-touch-icon.png
```

- - -
<!-- .slide: data-background-image="images/ship.jpg" --><!-- https://flic.kr/p/yMx2xK -->
`./requirements.yaml`

```yaml
dependencies:
  - name: mysql
    version: 0.2.1
    repository: http://storage.googleapis.com/kubernetes-charts
```

- - -
<!-- .slide: data-background-image="images/ship.jpg" --><!-- https://flic.kr/p/yMx2xK -->
`./templates/service.yaml`

```handlebars
apiVersion: v1
kind: Service
metadata:
  name: {{ template "fullname" . }}
  labels:
    chart: "{{ .Chart.Name }}-{{ .Chart.Version }}"
spec:
  type: {{ .Values.service.type }}
  ports:
  - port: {{ .Values.service.externalPort }}
    targetPort: {{ .Values.service.internalPort }}
    protocol: TCP
    name: {{ .Values.service.name }}
  selector:
    app: {{ template "fullname" . }}
```

[Sprig: Template functions for Go templates](https://github.com/Masterminds/sprig) <!-- .element: class="fragment" -->

#### Notes

* `Values`
* `Release.Name`, `Release.Time`, `Release.Namespace`, `Release.Service`
* `Chart`
* `Files`

- - -
<!-- .slide: data-background-image="images/ship.jpg" --><!-- https://flic.kr/p/yMx2xK -->
`./values.yaml`

```yaml
# Default values for symfony-demo.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.
replicaCount: 1
image:
  repository: mstrzele/symfony-demo
  tag: latest
  pullPolicy: Always
service:
  name: symfony-demo
  type: NodePort
  externalPort: 80
  internalPort: 8000
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi

framework:
  secret: secret_value_for_symfony_demo_application

mysql:
  mysqlUser: symfony-demo
  mysqlPassword: symfony-demo
  mysqlDatabase: symfony-demo
```

- - -
<!-- .slide: data-background-image="images/ship.jpg" --><!-- https://flic.kr/p/yMx2xK -->

```bash
$ find .
.
./index.yaml
./symfony-demo-0.1.0.tgz
```

- - -
<!-- .slide: data-background-image="images/ship.jpg" --><!-- https://flic.kr/p/yMx2xK -->
`./index.yaml`

```yaml
apiVersion: v1
entries:
  symfony-demo:
  - created: 2016-11-21T18:34:54.434552683+01:00
    description: Symfony Demo Application
    digest: a4a57aa9ef792fb00ff584f0b911b6e26fd45f60da74fec41df11392563407de
    engine: gotpl
    home: http://symfony.com/blog/introducing-the-symfony-demo-application
    icon: http://symfony.com/apple-touch-icon.png
    keywords:
    - symfony
    - demo
    maintainers:
    - email: maciej.strzelecki@gmail.com
      name: Maciej Strzelecki
    name: symfony-demo
    sources:
    - https://github.com/symfony/symfony-demo
    urls:
    - http://mstrzele-charts.s3-website-eu-west-1.amazonaws.com/symfony-demo-0.1.0.tgz
    version: 0.1.0
generated: 2016-11-21T18:34:54.433199567+01:00
```

---
<!-- .slide: data-background-image="images/ship-and-lashing-hooks.jpg" -->

```yaml
metadata:
  annotations:
    "helm.sh/hook": post-install,post-upgrade
```

#### Notes

* `Job`, **blocking operation**, the release will fail
* The order ... is not guaranteed

---
<!-- .slide: data-background-image="images/ibm-pc-xt-1983.jpg" --><!-- https://flic.kr/p/8RbMTs -->
### Installing Helm

```bash
brew cask install helm
```

```bash
$ curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get | bash
```
<!-- .element: class="fragment" -->

```bash
$ wget http://storage.googleapis.com/kubernetes-helm/helm-v2.0.0-linux-amd64.tar.gz
$ tar -zxvf helm-v2.0.0-linux-amd64.tgz
$ mv linux-amd64/helm /usr/local/bin/helm
```
<!-- .element: class="fragment" -->

- - -
<!-- .slide: data-background-image="images/ibm-pc-xt-1983.jpg" --><!-- https://flic.kr/p/8RbMTs -->
### Installing Tiller

```bash
$ helm init
Creating /Users/mstrzele/.helm
Creating /Users/mstrzele/.helm/repository
Creating /Users/mstrzele/.helm/repository/cache
Creating /Users/mstrzele/.helm/repository/local
Creating /Users/mstrzele/.helm/repository/repositories.yaml
Creating /Users/mstrzele/.helm/repository/local/index.yaml
$HELM_HOME has been configured at $HOME/.helm.

Tiller (the helm server side component) has been installed into your Kubernetes Cluster.
Happy Helming!
```

```bash
$ kubectl get pod --namespace kube-system
NAME                             READY     STATUS    RESTARTS   AGE
kube-addon-manager-minikube      1/1       Running   0          9m
kube-dns-v20-7vkd5               3/3       Running   0          8m
kubernetes-dashboard-guhij       1/1       Running   0          8m
tiller-deploy-2241983194-mbv1w   1/1       Running   0          8m
```
<!-- .element: class="fragment" -->

---
<!-- .slide: data-background-image="images/demo.jpg" --><!-- https://flic.kr/p/D323n -->
### Demo

#### Notes

* `kubectl config current-context`
* `helm version`
* `helm search mysql`
* `helm inspect stable/mysql`
* `cat dev.yaml`
* `cat qa.yaml`
* `helm install stable/mysql -n aws-ug-pl -f dev.yaml --version 0.2.0`
* `helm list`
* `helm get values aws-ug-pl`
* `helm get values aws-ug-pl -a`
* `helm upgrade aws-ug-pl stable/mysql -f dev.yaml --version 0.2.1`
* `helm history aws-ug-pl`
* `helm rollback aws-ug-pl 1`
* `helm delete aws-ug-pl`
* `helm list`
* `helm list --all`
* `helm delete --purge aws-ug-pl`

* `helm repo list`
* `helm repo add mstrzele http://charts.mstrzele.io/`
* `helm search mstrzele`

* `helm create symfony-demo`
* `ls -l symfony-demo`
* `helm package symfony-demo`
* `ls symfony-demo-0.1.0.tgz`

- - -
<!-- .slide: data-background-image="images/demo.jpg" --><!-- https://flic.kr/p/D323n -->

<asciinema-player cols="160" rows="30" src="demo.js" theme="solarized-dark"></asciinema-player>
[![asciicast](https://asciinema.org/a/64mommiwx41nsncmk6nj46b3x.png)](https://asciinema.org/a/64mommiwx41nsncmk6nj46b3x) <!-- .element: style="display: none" -->

---
<!-- .slide: data-background-image="images/student-audience.jpg" --><!-- https://flic.kr/p/byXhtx -->
### Questions?

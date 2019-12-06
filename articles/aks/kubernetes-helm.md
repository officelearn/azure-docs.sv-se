---
title: Distribuera behållare med Helm i Kubernetes på Azure
description: Lär dig hur du använder Helm packnings verktyg för att distribuera behållare i ett Azure Kubernetes service-kluster (AKS)
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 11/22/2019
ms.author: zarhoads
ms.openlocfilehash: 849f4e182d4bd9255ca8e60b69b6ad6c8a8e0fc7
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74869382"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Installera program med Helm i Azure Kubernetes service (AKS)

[Helm][helm] är ett paket med öppen källkod som hjälper dig att installera och hantera livs cykeln för Kubernetes-program. Precis som Linux-paket hanterare som *apt* och *yum*, används Helm för att hantera Kubernetes-diagram, som är paket med förkonfigurerade Kubernetes-resurser.

Den här artikeln visar hur du konfigurerar och använder Helm i ett Kubernetes-kluster på AKS.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Du behöver också Helm CLI installerat, som är den klient som körs i utvecklings systemet. Du kan starta, stoppa och hantera program med Helm. Om du använder Azure Cloud Shell är Helm CLI redan installerat. Installationsinstruktioner på din lokala plattform finns i [Installera Helm][helm-install].

> [!IMPORTANT]
> Helm är avsedd att köras på Linux-noder. Om du har Windows Server-noder i klustret måste du se till att Helm-poddar endast är schemalagda att köras på Linux-noder. Du måste också se till att alla Helm-diagram som du installerar också är schemalagda att köras på rätt noder. Kommandona i den här artikeln använder [Node-selectrs][k8s-node-selector] för att se till att poddar är schemalagda till rätt noder, men inte alla Helm-diagram kan visa en Node-selektor. Du kan också överväga att använda andra alternativ i klustret, till exempel för-och- [smak][taints].

## <a name="verify-your-version-of-helm"></a>Verifiera din version av Helm

Använd kommandot `helm version` för att kontrol lera vilken version av Helm du har installerat:

```console
helm version
```

I följande exempel visas Helm version 3.0.0 installerat:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

För Helm v3 följer du stegen i [avsnittet Helm v3](#install-an-application-with-helm-v3). För Helm v2 följer du stegen i [avsnittet Helm v2](#install-an-application-with-helm-v2)

## <a name="install-an-application-with-helm-v3"></a>Installera ett program med Helm v3

### <a name="add-the-official-helm-stable-charts-repository"></a>Lägg till lagrings platsen för officiella Helm stabila diagram

Använd kommandot [Helm lagrings platsen][helm-repo-add] för att lägga till officiella Helm stabila diagram-lagringsplatsen.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

### <a name="find-helm-charts"></a>Hitta Helm-diagram

Helm-diagram används för att distribuera program till ett Kubernetes-kluster. Om du vill söka efter tidigare skapade Helm-diagram använder du kommandot [Helm search][helm-search] :

```console
helm search repo stable
```

I följande komprimerade exempel utdata visas några av Helm-diagrammen som är tillgängliga för användning:


```console
$ helm search repo stable

NAME                                    CHART VERSION   APP VERSION                     DESCRIPTION                                       
stable/acs-engine-autoscaler            2.2.2           2.1.1                           DEPRECATED Scales worker nodes within agent pools 
stable/aerospike                        0.3.1           v4.5.0.5                        A Helm chart for Aerospike in Kubernetes          
stable/airflow                          4.10.0          1.10.4                          Airflow is a platform to programmatically autho...
stable/ambassador                       4.4.7           0.85.0                          A Helm chart for Datawire Ambassador              
stable/anchore-engine                   1.3.7           0.5.2                           Anchore container analysis and policy evaluatio...
stable/apm-server                       2.1.5           7.0.0                           The server receives data from the Elastic APM a...
stable/ark                              4.2.2           0.10.2                          DEPRECATED A Helm chart for ark                   
stable/artifactory                      7.3.1           6.1.0                           DEPRECATED Universal Repository Manager support...
stable/artifactory-ha                   0.4.1           6.2.0                           DEPRECATED Universal Repository Manager support...
stable/atlantis                         3.8.4           v0.8.2                          A Helm chart for Atlantis https://www.runatlant...
stable/auditbeat                        1.1.0           6.7.0                           A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler           0.3.3                                           Scales worker nodes within autoscaling groups.    
stable/aws-iam-authenticator            0.1.1           1.0                             A Helm chart for aws-iam-authenticator            
stable/bitcoind                         0.2.2           0.17.1                          Bitcoin is an innovative payment network and a ...
stable/bookstack                        1.1.2           0.27.4-1                        BookStack is a simple, self-hosted, easy-to-use...
stable/buildkite                        0.2.4           3                               DEPRECATED Agent for Buildkite                    
stable/burrow                           1.5.2           0.29.0                          Burrow is a permissionable smart contract machine 
stable/centrifugo                       3.1.0           2.1.0                           Centrifugo is a real-time messaging server.       
stable/cerebro                          1.3.1           0.8.5                           A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager                     v0.6.7          v0.6.2                          A Helm chart for cert-manager                     
stable/chaoskube                        3.1.2           0.14.0                          Chaoskube periodically kills random pods in you...
stable/chartmuseum                      2.4.0           0.8.2                           Host your own Helm Chart Repository               
stable/chronograf                       1.1.0           1.7.12                          Open-source web application written in Go and R...
stable/clamav                           1.0.4           1.6                             An Open-Source antivirus engine for detecting t...
stable/cloudserver                      1.0.3           8.1.5                           An open-source Node.js implementation of the Am...
stable/cluster-autoscaler               6.2.0           1.14.6                          Scales worker nodes within autoscaling groups.    
stable/cluster-overprovisioner          0.2.6           1.0                             Installs the a deployment that overprovisions t...
stable/cockroachdb                      2.1.16          19.1.5                          CockroachDB is a scalable, survivable, strongly...
stable/collabora-code                   1.0.5           4.0.3.1                         A Helm chart for Collabora Office - CODE-Edition  
stable/concourse                        8.2.7           5.6.0                           Concourse is a simple and scalable CI system.     
stable/consul                           3.9.2           1.5.3                           Highly available and distributed service discov...
stable/contour                          0.1.0           v0.15.0                         Contour Ingress controller for Kubernetes         
stable/coredns                          1.7.4           1.6.4                           CoreDNS is a DNS server that chains plugins and...
stable/cosbench                         1.0.1           0.0.6                           A benchmark tool for cloud object storage services
stable/coscale                          1.0.0           3.16.0                          CoScale Agent                                     
stable/couchbase-operator               1.0.1           1.2.1                           A Helm chart to deploy the Couchbase Autonomous...
stable/couchdb                          2.3.0           2.3.1                           DEPRECATED A database featuring seamless multi-...
stable/dask                             3.1.0           1.1.5                           Distributed computation in Python with task sch...
stable/dask-distributed                 2.0.2                                           DEPRECATED: Distributed computation in Python     
stable/datadog                          1.38.3          6.14.0                          DataDog Agent 
...
```

Om du vill uppdatera listan över diagram använder du kommandot [Helm lagrings platsen Update][helm-repo-update] . I följande exempel visas en lyckad lagrings platsen-uppdatering:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Köra Helm-diagram

Om du vill installera diagram med Helm använder du kommandot [Helm install][helm-install-command] och anger ett versions namn och namnet på det diagram som ska installeras. Om du vill se hur du installerar ett Helm-diagram i praktiken ska vi installera en grundläggande nginx-distribution med ett Helm-diagram.

```console
helm install my-nginx-ingress stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

I följande komprimerade exempel utdata visas distributions status för de Kubernetes-resurser som skapats av Helm-diagrammet:

```console
$ helm install my-nginx-ingress stable/nginx-ingress \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-controller'
...
```

Använd kommandot `kubectl get services` för att hämta den *externa IP-adressen* för din tjänst. Till exempel visar kommandot nedan den *externa IP-adressen* för tjänsten *My-nginx-* ingångs-Controller:

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-controller

NAME                          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
my-nginx-ingress-controller   LoadBalancer   10.0.123.1     <EXTERNAL-IP>   80:31301/TCP,443:31623/TCP   96s   app=nginx-ingress,component=controller,release=my-nginx-ingress
```

### <a name="list-releases"></a>Lista över versioner

Om du vill se en lista över versioner som är installerade i klustret använder du kommandot `helm list`.

```console
helm list
```

I följande exempel visas den *nginx-* ingångs version som distribuerades i föregående steg:

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>Rensa resurser

När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. Dessa resurser omfattar poddar, distributioner och tjänster. Om du vill rensa resurserna använder du kommandot [Helm Uninstall][helm-cleanup] och anger namnet på din utgåva, som du hittar i föregående `helm list`-kommando.

```console
helm uninstall my-nginx-ingress
```

I följande exempel visas versionen som heter *My-nginx-* ingångs funktionen har avinstallerats:

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="install-an-application-with-helm-v2"></a>Installera ett program med Helm v2

### <a name="create-a-service-account"></a>Skapa ett tjänst konto

Innan du kan distribuera Helm i ett RBAC-aktiverat AKS-kluster behöver du ett tjänst konto och en roll bindning för till-tjänsten. Mer information om hur du skyddar Helm/till i ett RBAC-aktiverat kluster finns i [till exempel, namnrymder och RBAC][tiller-rbac]. Om ditt AKS-kluster inte är RBAC-aktiverat, hoppar du över det här steget.

Skapa en fil med namnet `helm-rbac.yaml` och kopiera i följande YAML:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Skapa tjänst kontot och roll bindningen med kommandot `kubectl apply`:

```console
kubectl apply -f helm-rbac.yaml
```

### <a name="secure-tiller-and-helm"></a>Skydda till-och Helm

Helm-klienten och till-tjänsten autentiserar och kommunicerar med varandra med TLS/SSL. Den här autentiseringsmetoden hjälper till att skydda Kubernetes-klustret och vilka tjänster som kan distribueras. Du kan förbättra säkerheten genom att skapa egna signerade certifikat. Varje Helm-användare får sitt eget klient certifikat och till gången initieras i Kubernetes-klustret med certifikat som tillämpas. Mer information finns i [using TLS/SSL mellan Helm och till][helm2-ssl].

Med ett RBAC-aktiverat Kubernetes-kluster kan du styra åtkomst nivån till klustret. Du kan definiera Kubernetes-namnområdet som används för att distribueras i och begränsa vilka namn områden som ska användas för att distribuera resurser i. Med den här metoden kan du skapa till-instanser i olika namn områden och begränsa distributions gränser och omfånget användare av Helm-klienten till vissa namn områden. Mer information finns i [Helm-rollbaserade åtkomst kontroller][helm2-rbac].

### <a name="configure-helm"></a>Konfigurera Helm

Om du vill distribuera en Basic-till-till-AKS-kluster använder du kommandot [Helm init][helm2-init] . Om klustret inte är RBAC-aktiverat tar du bort `--service-account` argument och värde. I följande exempel anger du även [historiken-Max][helm2-history-max] till 200.

Om du har konfigurerat TLS/SSL för till gång och Helm, hoppar du över detta grundläggande initierings steg och anger i stället de nödvändiga `--tiller-tls-` som visas i nästa exempel.

```console
helm init --history-max 200 --service-account tiller --node-selectors "beta.kubernetes.io/os=linux"
```

Om du har konfigurerat TLS/SSL mellan Helm och till, anger du `--tiller-tls-*` parametrar och namn för dina egna certifikat, som du ser i följande exempel:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --history-max 200 \
    --service-account tiller \
    --node-selectors "beta.kubernetes.io/os=linux"
```

### <a name="find-helm-charts"></a>Hitta Helm-diagram

Helm-diagram används för att distribuera program till ett Kubernetes-kluster. Om du vill söka efter tidigare skapade Helm-diagram använder du kommandot [Helm search][helm2-search] :

```console
helm search
```

I följande komprimerade exempel utdata visas några av Helm-diagrammen som är tillgängliga för användning:

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

Om du vill uppdatera listan över diagram använder du kommandot [Helm lagrings platsen Update][helm2-repo-update] . I följande exempel visas en lyckad lagrings platsen-uppdatering:

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete.
```

### <a name="run-helm-charts"></a>Köra Helm-diagram

Om du vill installera diagram med Helm använder du kommandot [Helm install][helm2-install-command] och anger namnet på det diagram som ska installeras. Om du vill se hur du installerar ett Helm-diagram i praktiken ska vi installera en grundläggande nginx-distribution med ett Helm-diagram. Om du har konfigurerat TLS/SSL lägger du till parametern `--tls` för att använda klient certifikatet för Helm.

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

I följande komprimerade exempel utdata visas distributions status för de Kubernetes-resurser som skapats av Helm-diagrammet:

```
$ helm install stable/nginx-ingress --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME:   flailing-alpaca
LAST DEPLOYED: Thu May 23 12:55:21 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                      DATA  AGE
flailing-alpaca-nginx-ingress-controller  1     0s

==> v1/Pod(related)
NAME                                                            READY  STATUS             RESTARTS  AGE
flailing-alpaca-nginx-ingress-controller-56666dfd9f-bq4cl       0/1    ContainerCreating  0         0s
flailing-alpaca-nginx-ingress-default-backend-66bc89dc44-m87bp  0/1    ContainerCreating  0         0s

==> v1/Service
NAME                                           TYPE          CLUSTER-IP  EXTERNAL-IP  PORT(S)                     AGE
flailing-alpaca-nginx-ingress-controller       LoadBalancer  10.0.109.7  <pending>    80:31219/TCP,443:32421/TCP  0s
flailing-alpaca-nginx-ingress-default-backend  ClusterIP     10.0.44.97  <none>       80/TCP                      0s
...
```

Det tar en minut eller två för den *externa IP* -adressen för nginx-tjänsten att fyllas i och ger dig åtkomst till den med en webbläsare.

### <a name="list-helm-releases"></a>Visa lista Helm-versioner

Om du vill se en lista över installerade versioner i klustret använder du kommandot [Helm List][helm2-list] . I följande exempel visas den nginx-ingångs version som distribuerats i föregående steg. Om du har konfigurerat TLS/SSL lägger du till parametern `--tls` för att använda klient certifikatet för Helm.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

### <a name="clean-up-resources"></a>Rensa resurser

När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. Dessa resurser omfattar poddar, distributioner och tjänster. Om du vill rensa resurserna använder du kommandot `helm delete` och anger namnet på din version, som du hittar i föregående `helm list`-kommando. I följande exempel tar vi bort versionen med namnet *Flailing-Alpaca*:

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar Kubernetes program distributioner med Helm finns i Helm-dokumentationen.

> [!div class="nextstepaction"]
> [Dokumentation om Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm2-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm2-install-command]: https://v2.helm.sh/docs/helm/#helm-install
[helm2-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm2-list]: https://v2.helm.sh/docs/helm/#helm-list
[helm2-history-max]: https://v2.helm.sh/docs/using_helm/#initialize-helm-and-install-tiller
[helm2-rbac]: https://v2.helm.sh/docs/using_helm/#role-based-access-control
[helm2-repo-update]: https://v2.helm.sh/docs/helm/#helm-repo-update
[helm2-search]: https://v2.helm.sh/docs/helm/#helm-search
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
[helm2-ssl]: https://v2.helm.sh/docs/using_helm/#using-ssl-between-helm-and-tiller
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
[taints]: operator-best-practices-advanced-scheduler.md

---
title: Distribuera behållare med Helm i Kubernetes på Azure
description: Lär dig hur du använder Helm-förpackningsverktyget för att distribuera behållare i ett AKS-kluster (Azure Kubernetes Service)
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/22/2019
ms.author: zarhoads
ms.openlocfilehash: 4a9ccaff0e3425c365a64ecb4fbadf3c7aa8dcfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595186"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Installera program med Helm i Azure Kubernetes Service (AKS)

[Helm][helm] är ett förpackningsverktyg med öppen källkod som hjälper dig att installera och hantera livscykeln för Kubernetes-program. I likhet med Linux-pakethanterare som *APT* och *Yum*används Helm för att hantera Kubernetes-diagram, som är paket med förkonfigurerade Kubernetes-resurser.

Den här artikeln visar hur du konfigurerar och använder Helm i ett Kubernetes-kluster på AKS.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster läser du SNABBSTARTen för AKS [med Azure CLI][aks-quickstart-cli] eller använder [Azure-portalen][aks-quickstart-portal].

Du behöver också Helm CLI installerat, som är den klient som körs på ditt utvecklingssystem. Det gör att du kan starta, stoppa och hantera program med Helm. Om du använder Azure Cloud Shell är Helm CLI redan installerat. Installationsinstruktioner på din lokala plattform finns i [Installera Helm][helm-install].

> [!IMPORTANT]
> Helm är avsett att köras på Linux-noder. Om du har Windows Server-noder i klustret måste du se till att Helm-poddar endast är schemalagda att köras på Linux-noder. Du måste också se till att alla Helm-diagram som du installerar också är schemalagda att köras på rätt noder. Kommandona i den här artikeln använder nodväljare för att se till att poddar är schemalagda till rätt noder, men alla [Helm-diagram][k8s-node-selector] kan inte visa en nodväljare. Du kan också överväga att använda andra alternativ i klustret, till exempel [taints][taints].

## <a name="verify-your-version-of-helm"></a>Verifiera din version av Helm

Använd `helm version` kommandot för att verifiera vilken version av Helm du har installerat:

```console
helm version
```

I följande exempel visas Helm version 3.0.0 installerat:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

För Helm v3 följer du stegen i [Helm v3-avsnittet](#install-an-application-with-helm-v3). För Helm v2 följer du stegen i [helm v2-avsnittet](#install-an-application-with-helm-v2)

## <a name="install-an-application-with-helm-v3"></a>Installera ett program med Helm v3

### <a name="add-the-official-helm-stable-charts-repository"></a>Lägg till den officiella Helm-databasen för stabila diagram

Använd kommandot [helm repo][helm-repo-add] för att lägga till den officiella Helm-databasen för stabila diagram.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

### <a name="find-helm-charts"></a>Hitta Helm-diagram

Helm-diagram används för att distribuera program till ett Kubernetes-kluster. Om du vill söka efter förskapade Helm-diagram använder du kommandot [helm search:][helm-search]

```console
helm search repo stable
```

Följande komprimerade exempelutdata visar några av Helm-diagrammen som är tillgängliga för användning:


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

Om du vill uppdatera listan med diagram använder du kommandot för uppdatering av [helm repo.][helm-repo-update] I följande exempel visas en lyckad repouppdatering:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Kör Helm-diagram

Om du vill installera diagram med Helm använder du kommandot [helm install][helm-install-command] och anger ett versionsnamn och namnet på det diagram som ska installeras. Om du vill se installera ett Helm-diagram i aktion ska vi installera en grundläggande nginx-distribution med hjälp av ett Helm-diagram.

```console
helm install my-nginx-ingress stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Följande komprimerade exempelutdata visar distributionsstatusen för kubernetes-resurser som skapats av Helm-diagrammet:

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

Använd `kubectl get services` kommandot för att hämta *extern IP för* din tjänst. Kommandot nedan visar till exempel *extern IP* för tjänsten *my-nginx-ingress-controller:*

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-controller

NAME                          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
my-nginx-ingress-controller   LoadBalancer   10.0.123.1     <EXTERNAL-IP>   80:31301/TCP,443:31623/TCP   96s   app=nginx-ingress,component=controller,release=my-nginx-ingress
```

### <a name="list-releases"></a>Listutgåvor

Om du vill visa en lista över `helm list` versioner som är installerade i klustret använder du kommandot.

```console
helm list
```

I följande exempel visas den *min-nginx-ingress-versionen* som distribuerats i föregående steg:

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>Rensa resurser

När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. Dessa resurser omfattar poddar, distributioner och tjänster. Om du vill rensa dessa resurser använder du kommandot [helm uninstall][helm-cleanup] `helm list` och anger ditt versionsnamn, enligt föregående kommando.

```console
helm uninstall my-nginx-ingress
```

I följande exempel visas att utgåvan med namnet *my-nginx-ingress* har avinstallerats:

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="install-an-application-with-helm-v2"></a>Installera ett program med Helm v2

### <a name="create-a-service-account"></a>Skapa ett tjänstkonto

Innan du kan distribuera Helm i ett RBAC-aktiverat AKS-kluster behöver du ett tjänstkonto och rollbindning för Tiller-tjänsten. Mer information om hur du skyddar Helm/Tiller i ett RBAC-aktiverat kluster finns i [Tiller, Namespaces och RBAC][tiller-rbac]. Om AKS-klustret inte är RBAC aktiverat hoppar du över det här steget.

Skapa en `helm-rbac.yaml` fil med namnet och kopiera i följande YAML:

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

Skapa tjänstkontot och rollbindningen `kubectl apply` med kommandot:

```console
kubectl apply -f helm-rbac.yaml
```

### <a name="secure-tiller-and-helm"></a>Säker rorkult och Helm

Helm-klienten och Tiller-tjänsten autentiserar och kommunicerar med varandra med TLS/SSL. Den här autentiseringsmetoden hjälper till att skydda Kubernetes-klustret och vilka tjänster som kan distribueras. För att förbättra säkerheten kan du skapa egna signerade certifikat. Varje Helm-användare skulle få sitt eget klientcertifikat och Tiller skulle initieras i Kubernetes-klustret med certifikat som tillämpas. Mer information finns i [Använda TLS/SSL mellan Helm och Tiller][helm2-ssl].

Med ett RBAC-aktiverat Kubernetes-kluster kan du styra åtkomstnivån Tiller har till klustret. Du kan definiera namnområdet Kubernetes som Tiller distribueras i och begränsa vilka namnområden Tiller sedan kan distribuera resurser i. Med den här metoden kan du skapa Tiller-instanser i olika namnområden och begränsa distributionsgränserna och begränsa användarna av Helm-klienten till vissa namnområden. Mer information finns i [Helm-rollbaserade åtkomstkontroller][helm2-rbac].

### <a name="configure-helm"></a>Konfigurera Helm

Om du vill distribuera en grundläggande tiller i ett AKS-kluster använder du kommandot [helm init.][helm2-init] Om klustret inte är AKTIVERAT `--service-account` FÖR RBAC tar du bort argumentet och värdet. I följande exempel anges också [historikmaximen][helm2-history-max] till 200.

Om du har konfigurerat TLS/SSL för Rorkult och Helm hoppar `--tiller-tls-` du över det här grundläggande initieringssteget och anger i stället det nödvändiga som krävs i nästa exempel.

```console
helm init --history-max 200 --service-account tiller --node-selectors "beta.kubernetes.io/os=linux"
```

Om du har konfigurerat TLS/SSL mellan `--tiller-tls-*` Helm och Tiller anger du parametrar och namn på dina egna certifikat, vilket visas i följande exempel:

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

Helm-diagram används för att distribuera program till ett Kubernetes-kluster. Om du vill söka efter förskapade Helm-diagram använder du kommandot [helm search:][helm2-search]

```console
helm search
```

Följande komprimerade exempelutdata visar några av Helm-diagrammen som är tillgängliga för användning:

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

Om du vill uppdatera listan med diagram använder du kommandot för uppdatering av [helm repo.][helm2-repo-update] I följande exempel visas en lyckad repouppdatering:

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete.
```

### <a name="run-helm-charts"></a>Kör Helm-diagram

Om du vill installera diagram med Helm använder du kommandot [helm install][helm2-install-command] och anger namnet på det diagram som ska installeras. Om du vill se installera ett Helm-diagram i aktion ska vi installera en grundläggande nginx-distribution med hjälp av ett Helm-diagram. Om du har konfigurerat TLS/SSL lägger du till parametern `--tls` för att använda Helm-klientcertifikatet.

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Följande komprimerade exempelutdata visar distributionsstatusen för kubernetes-resurser som skapats av Helm-diagrammet:

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

Det tar en minut eller två för *extern IP-adressen* för tjänsten nginx-ingress-controller som ska fyllas i och gör att du kan komma åt den med en webbläsare.

### <a name="list-helm-releases"></a>Lista Helm släpper

Om du vill visa en lista över versioner som är installerade i klustret använder du kommandot [helm list.][helm2-list] I följande exempel visas nginx-ingress-versionen som distribuerats i föregående steg. Om du har konfigurerat TLS/SSL lägger du till parametern `--tls` för att använda Helm-klientcertifikatet.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

### <a name="clean-up-resources"></a>Rensa resurser

När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. Dessa resurser omfattar poddar, distributioner och tjänster. Om du vill rensa `helm delete` dessa resurser använder du kommandot och `helm list` anger ditt versionsnamn, enligt föregående kommando. I följande exempel tas utgivningen *flailing-alpacka*bort:

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar Kubernetes programdistributioner med Helm finns i Helm-dokumentationen.

> [!div class="nextstepaction"]
> [Dokumentation för helm][helm-documentation]

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

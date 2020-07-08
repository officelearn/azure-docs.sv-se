---
title: Installera befintliga program med Helm i AKS
description: Lär dig hur du använder Helm packnings verktyg för att distribuera behållare i ett Azure Kubernetes service-kluster (AKS)
services: container-service
author: zr-msft
ms.topic: article
ms.date: 06/24/2020
ms.author: zarhoads
ms.openlocfilehash: 6ee99eee02e874208106d39c6442f54f59f95dad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361616"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Installera befintliga program med Helm i Azure Kubernetes service (AKS)

[Helm][helm] är ett paket med öppen källkod som hjälper dig att installera och hantera livs cykeln för Kubernetes-program. Precis som Linux-paket hanterare som *apt* och *yum*, används Helm för att hantera Kubernetes-diagram, som är paket med förkonfigurerade Kubernetes-resurser.

Den här artikeln visar hur du konfigurerar och använder Helm i ett Kubernetes-kluster på AKS.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Du behöver också Helm CLI installerat, som är den klient som körs i utvecklings systemet. Du kan starta, stoppa och hantera program med Helm. Om du använder Azure Cloud Shell är Helm CLI redan installerat. Installationsinstruktioner på din lokala plattform finns i [Installera Helm][helm-install].

> [!IMPORTANT]
> Helm är avsedd att köras på Linux-noder. Om du har Windows Server-noder i klustret måste du se till att Helm-poddar endast är schemalagda att köras på Linux-noder. Du måste också se till att alla Helm-diagram som du installerar också är schemalagda att köras på rätt noder. Kommandona i den här artikeln använder [Node-Selector] [K8s-Node-Selector] för att se till att poddar är schemalagda till rätt noder, men inte alla Helm-diagram kan exponera en Node-selektor. Du kan också överväga att använda andra alternativ i klustret, till exempel för-och- [smak][taints].

## <a name="verify-your-version-of-helm"></a>Verifiera din version av Helm

Använd `helm version` kommandot för att kontrol lera att du har Helm 3 installerat:

```console
helm version
```

I följande exempel visas Helm version 3.0.0 installerat:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

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

Om du vill uppdatera listan över diagram använder du kommandot [Helm lagrings platsen Update][helm-repo-update] .

```console
helm repo update
```

I följande exempel visas en lyckad lagrings platsen-uppdatering:

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

Använd `kubectl get services` kommandot för att hämta den *externa IP-adressen* för din tjänst.

```console
kubectl --namespace default get services -o wide -w my-nginx-ingress-controller
```

Till exempel visar kommandot nedan den *externa IP-adressen* för tjänsten *My-nginx-* ingångs-Controller:

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-controller

NAME                          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
my-nginx-ingress-controller   LoadBalancer   10.0.123.1     <EXTERNAL-IP>   80:31301/TCP,443:31623/TCP   96s   app=nginx-ingress,component=controller,release=my-nginx-ingress
```

### <a name="list-releases"></a>Lista över versioner

Om du vill se en lista över versioner som är installerade i klustret använder du `helm list` kommandot.

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

När du distribuerar ett Helm-diagram skapas ett antal Kubernetes-resurser. Dessa resurser omfattar poddar, distributioner och tjänster. Om du vill rensa resurserna använder du kommandot [Helm Uninstall][helm-cleanup] och anger namnet på din utgåva, som du hittar i föregående `helm list` kommando.

```console
helm uninstall my-nginx-ingress
```

I följande exempel visas versionen som heter *My-nginx-* ingångs funktionen har avinstallerats:

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
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
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md

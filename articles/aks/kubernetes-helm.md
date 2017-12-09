---
title: "Distribuera behållare med Helm i Kubernetes på Azure"
description: "Verktyget Helm paketering ska distribuera behållare på ett kluster med Kubernetes på AKS"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 002c4376c91f4a7176cc9b00a4d6ba275f87dadb
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="use-helm-with-azure-container-service-aks"></a>Använda Helm med Azure Container Service (AKS)

[Helm](https://github.com/kubernetes/helm/) är en öppen källkod paketering verktyg som hjälper dig att installera och hantera livscykeln för Kubernetes program. Liknar Linux paketet chefer som *LGH* och *Yum*, Helm används för att hantera Kubernetes diagram som är paket med förkonfigurerade Kubernetes resurser.

Det här dokumentet steg genom att konfigurera och använda Helm i ett Kubernetes kluster på AKS.

## <a name="before-you-begin"></a>Innan du börjar

Stegen som beskrivs i det här dokumentet förutsätter att du har skapat ett AKS-kluster och har upprättat en kubectl-anslutning med klustret. Se [AKS-snabbstart](./kubernetes-walkthrough.md) om du behöver de här objekten.

## <a name="install-helm-cli"></a>Installera Helm CLI

Helm CLI är en klient som körs på utvecklingssystemet och gör att du kan starta, stoppa och hantera program med Helm diagram.

Om du använder Azure CloudShell är Helm CLI redan installerad. Installera Helm CLI på en Mac-Använd `brew`. Installation av ytterligare alternativ finns [installerar Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md).

```console
brew install kubernetes-helm
```

Resultat:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="configure-helm"></a>Konfigurera Helm

Den [helm init](https://docs.helm.sh/helm/#helm-init) kommandot används för att installera Helm komponenter i ett Kubernetes kluster och ge klientsidan konfigurationer. Kör följande kommando för att installera Helm på AKS klustret och konfigurera Helm-klienten.

```azurecli-interactive
helm init
```

Resultat:

```
$HELM_HOME has been configured at /Users/user/.helm.
Not installing Tiller due to 'client-only' flag having been set
Happy Helming!
```

## <a name="find-helm-charts"></a>Hitta Helm diagram

Helm scheman används för att distribuera program till ett Kubernetes-kluster. Om du vill söka i förväg skapade Helm diagram använder den [helm Sök](https://docs.helm.sh/helm/#helm-search) kommando.

```azurecli-interactive
helm search
```

Utdata söker liknande exemplen nedan, men många flera diagram.

```
NAME                            VERSION DESCRIPTION
stable/acs-engine-autoscaler    2.0.0   Scales worker nodes within agent pools
stable/artifactory              6.1.0   Universal Repository Manager supporting all maj...
stable/aws-cluster-autoscaler   0.3.1   Scales worker nodes within autoscaling groups.
stable/buildkite                0.2.0   Agent for Buildkite
stable/centrifugo               2.0.0   Centrifugo is a real-time messaging server.
stable/chaoskube                0.5.0   Chaoskube periodically kills random pods in you...
stable/chronograf               0.3.0   Open-source web application written in Go and R...
stable/cluster-autoscaler       0.2.0   Scales worker nodes within autoscaling groups.
stable/cockroachdb              0.5.0   CockroachDB is a scalable, survivable, strongly...
stable/concourse                0.7.0   Concourse is a simple and scalable CI system.
stable/consul                   0.4.1   Highly available and distributed service discov...
stable/coredns                  0.5.0   CoreDNS is a DNS server that chains middleware ...
stable/coscale                  0.2.0   CoScale Agent
stable/dask-distributed         2.0.0   Distributed computation in Python
stable/datadog                  0.8.0   DataDog Agent
...
```

Uppdatera listan över diagram med de [helm lagringsplatsen uppdatering](https://docs.helm.sh/helm/#helm-repo-update) kommando.

```azurecli-interactive
helm repo update
```

Resultat:

```
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Kör Helm diagram

Om du vill distribuera en domänkontrollant för en NGINX-ingång använder den [helm installera](https://docs.helm.sh/helm/#helm-install) kommando.

```azurecli-interactive
helm install stable/nginx-ingress
```

Utdata liknar följande, men innehåller ytterligare information, till exempel instruktioner om hur du använder Kubernetes-distributionen.

```
NAME:   tufted-ocelot
LAST DEPLOYED: Thu Oct  5 00:48:04 2017
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                    DATA  AGE
tufted-ocelot-nginx-ingress-controller  1     5s

==> v1/Service
NAME                                         CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
tufted-ocelot-nginx-ingress-controller       10.0.140.10  <pending>    80:30486/TCP,443:31358/TCP  5s
tufted-ocelot-nginx-ingress-default-backend  10.0.34.132  <none>       80/TCP                      5s

==> v1beta1/Deployment
NAME                                         DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
tufted-ocelot-nginx-ingress-controller       1        1        1           0          5s
tufted-ocelot-nginx-ingress-default-backend  1        1        1           1          5s
...
```

Läs mer om hur du använder en domänkontrollant för en NGINX-ingång med Kubernetes [NGINX ingång Controller](https://github.com/kubernetes/ingress/tree/master/controllers/nginx).

## <a name="list-helm-charts"></a>Lista Helm diagram

Om du vill se en lista över diagram som installerats på klustret använder den [helm lista](https://docs.helm.sh/helm/#helm-list) kommando.

```azurecli-interactive
helm list
```

Resultat:

```
NAME            REVISION    UPDATED                     STATUS      CHART               NAMESPACE
bilging-ant     1           Thu Oct  5 00:11:11 2017    DEPLOYED    nginx-ingress-0.8.7 default
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar Kubernetes diagram i dokumentationen om Helm.

> [!div class="nextstepaction"]
> [Helm dokumentation](https://github.com/kubernetes/helm/blob/master/docs/index.md)
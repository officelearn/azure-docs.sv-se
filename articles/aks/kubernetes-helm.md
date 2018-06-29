---
title: Distribuera behållare med Helm i Kubernetes på Azure
description: Verktyget Helm paketering ska distribuera behållare på ett kluster med Kubernetes på AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 531e6d9368b2bf91c48fd41b1e9330879b0df49a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102485"
---
# <a name="use-helm-with-azure-kubernetes-service-aks"></a>Använda Helm med Azure Kubernetes-tjänsten (AKS)

[Helm] [ helm] är en öppen källkod paketering verktyg som hjälper dig att installera och hantera livscykeln för Kubernetes program. Liknar Linux paketet chefer som *LGH* och *Yum*, Helm används för att hantera Kubernetes diagram som är paket med förkonfigurerade Kubernetes resurser.

Det här dokumentet steg genom att konfigurera och använda Helm i ett Kubernetes kluster på AKS.

## <a name="before-you-begin"></a>Innan du börjar

Stegen som beskrivs i det här dokumentet förutsätter att du har skapat ett AKS-kluster och har upprättat en kubectl-anslutning med klustret. Om du behöver dessa objekt visas den [AKS quickstart][aks-quickstart].

## <a name="install-helm-cli"></a>Installera Helm CLI

Helm CLI är en klient som körs på utvecklingssystemet och gör att du kan starta, stoppa och hantera program med Helm.

Om du använder Azure CloudShell är Helm CLI redan installerad. Installera Helm CLI på en Mac-Använd `brew`. Installation av ytterligare alternativ finns [installerar Helm][helm-install-options].

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

## <a name="create-service-account"></a>Skapa tjänstkonto

Innan konfigurera Helm i en RBAC aktiverad klustret, behöver du ett tjänstkonto och rollen bindning för tjänsten rorkulten. Mer information om hur du skyddar Helm / rorkulten i en RBAC aktiverat kluster, se [rorkulten namnområden och RBAC][tiller-rbac]. Observera om klustret inte är RBAC aktiverad, hoppa över det här steget.

Skapa en fil med namnet `helm-rbac.yaml` och kopiera följande YAML.

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
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

Skapar tjänstkonto och rollen bindning med det `kubectl create` kommando.

```
kubectl create -f helm-rbac.yaml
```

När klustret med en RBAC aktiverad, har du alternativ för åtkomstnivån som rorkulten har till klustret. Se [Helm: rollbaserade åtkomstkontroller] [ helm-rbac] mer information om konfigurationsalternativ.

## <a name="configure-helm"></a>Konfigurera Helm

Nu installeras rorkulten med den [helm init] [ helm-init] kommando. Om klustret inte är aktiverat RBAC kan ta bort den `--service-account` argument och dess värde.

```
helm init --service-account tiller
```

## <a name="find-helm-charts"></a>Hitta Helm diagram

Helm scheman används för att distribuera program till ett Kubernetes-kluster. Om du vill söka i förväg skapade Helm diagram använder den [helm Sök] [ helm-search] kommando.

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

Uppdatera listan över diagram med de [helm lagringsplatsen uppdatering] [ helm-repo-update] kommando.

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

Distribuera Wordpress med hjälp av ett Helm diagram med hjälp av [helm installera] [ helm-install] kommando.

```azurecli-interactive
helm install stable/wordpress
```

Utdata liknar följande, men innehåller ytterligare information, till exempel instruktioner om hur du använder Kubernetes-distributionen.

```
NAME:   bilging-ibex
LAST DEPLOYED: Tue Jun  5 14:31:49 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                     READY  STATUS   RESTARTS  AGE
bilging-ibex-mariadb-7557b5474-dmdxn     0/1    Pending  0         1s
bilging-ibex-wordpress-7494c545fb-tskhz  0/1    Pending  0         1s

==> v1/Secret
NAME                    TYPE    DATA  AGE
bilging-ibex-mariadb    Opaque  2     1s
bilging-ibex-wordpress  Opaque  2     1s

==> v1/ConfigMap
NAME                        DATA  AGE
bilging-ibex-mariadb        1     1s
bilging-ibex-mariadb-tests  1     1s

==> v1/PersistentVolumeClaim
NAME                    STATUS   VOLUME   CAPACITY  ACCESS MODES  STORAGECLASS  AGE
bilging-ibex-mariadb    Pending  default  1s
bilging-ibex-wordpress  Pending  default  1s

==> v1/Service
NAME                    TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)                     AGE
bilging-ibex-mariadb    ClusterIP     10.0.76.164   <none>       3306/TCP                    1s
bilging-ibex-wordpress  LoadBalancer  10.0.215.250  <pending>    80:30934/TCP,443:31134/TCP  1s

==> v1beta1/Deployment
NAME                    DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
bilging-ibex-mariadb    1        1        1           0          1s
bilging-ibex-wordpress  1        1        1           0          1s
...
```

## <a name="list-helm-releases"></a>Lista Helm versioner

Om du vill se en lista över versioner som är installerade på klustret använder den [helm lista] [ helm-list] kommando.

```azurecli-interactive
helm list
```

Resultat:

```
NAME            REVISION    UPDATED                     STATUS      CHART           NAMESPACE
bilging-ibex    1           Tue Jun  5 14:31:49 2018    DEPLOYED    wordpress-1.0.9 default
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar Kubernetes diagram i dokumentationen om Helm.

> [!div class="nextstepaction"]
> [Helm dokumentation][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/helm/#helm-install
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md

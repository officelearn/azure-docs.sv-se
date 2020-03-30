---
title: Installera konsul i Azure Kubernetes Service (AKS)
description: Lär dig hur du installerar och använder Consul för att skapa ett tjänstnät i ett AKS-kluster (Azure Kubernetes Service)
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 1601ab6d81b888fd2247e95f22c58e1fc91df698
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273727"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Installera och använda konsul i Azure Kubernetes Service (AKS)

[Consul][consul-github] är ett servicenät med öppen källkod som tillhandahåller en nyckeluppsättning funktioner över mikrotjänsterna i ett Kubernetes-kluster. Dessa funktioner omfattar tjänstidentifiering, hälsokontroll, segmentering av tjänster och observerbarhet. Mer information om konsul finns i den officiella dokumentationen [Vad är konsul.][consul-docs-concepts]

Den här artikeln visar hur du installerar konsul. Consul-komponenterna installeras i ett Kubernetes-kluster på AKS.

> [!NOTE]
> Dessa instruktioner refererar `1.6.0`till Consul-versionen `2.14.2`och använder åtminstone Helm-versionen .
>
> Consul-utgåvorna `1.6.x` kan köras mot `1.13+`Kubernetes-versioner . Du hittar ytterligare konsulversioner på [GitHub - Consul Releases][consul-github-releases] och information om var och en av utgåvorna på [Consul- Release Notes][consul-release-notes].

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Installera consul-komponenterna på AKS
> * Validera konsulinstallationen
> * Avinstallera konsul från AKS

## <a name="before-you-begin"></a>Innan du börjar

Stegen som beskrivs i den här artikeln förutsätter att du `1.13` har skapat ett AKS-kluster (Kubernetes och högre, med RBAC aktiverat) och har upprättat en `kubectl` anslutning till klustret. Om du behöver hjälp med något av dessa objekt, sedan se [AKS snabbstart][aks-quickstart]. Kontrollera att klustret har minst 3 noder i Linux-nodpoolen.

Du behöver [Helm][helm] för att följa dessa instruktioner och installera konsuln. Vi rekommenderar att du har den senaste stabila versionen korrekt installerad och konfigurerad i klustret. Om du behöver hjälp med att installera Helm, se [AKS Helm installationsvägledning][helm-install]. Alla konsulkapslar måste också schemaläggas för att köras på Linux-noder.

Den här artikeln separerar konsulinstallationsvägledningen i flera diskreta steg. Slutresultatet är detsamma i struktur som den officiella konsulinstallation [vägledning][consul-install-k8].

### <a name="install-the-consul-components-on-aks"></a>Installera consul-komponenterna på AKS

Vi börjar med att `v0.10.0` ladda ner versionen av konsul helm-listan. Den här versionen av diagrammet `1.6.0`innehåller Consul version .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

Använd Helm och `consul-helm` det nedladdade diagrammet för `consul` att installera consul-komponenterna i namnområdet i AKS-klustret. 

> [!NOTE]
> **Installationsalternativ**
> 
> Vi använder följande alternativ som en del av vår installation:
> - `connectInject.enabled=true`- göra det möjligt att injicera proxyservrar i skida
> - `client.enabled=true`- göra det möjligt för konsulklienter att köras på varje nod
> - `client.grpc=true`- aktivera gRPC-lyssnare för connectInject
> - `syncCatalog.enabled=true`- synkronisera Kubernetes och konsultjänster
>
> **Nodväljare**
>
> Konsuln måste för närvarande schemaläggas för att köras på Linux-noder. Om du har Windows Server-noder i klustret måste du se till att konsulpodderna endast är schemalagda att köras på Linux-noder. Vi använder [nodväljare][kubernetes-node-selectors] för att se till att poddar är schemalagda till rätt noder.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

`Consul` Helm-diagrammet distribuerar ett antal objekt. Du kan se listan från `helm install` utdata från kommandot ovan. Distributionen av consul-komponenterna kan ta cirka 3 minuter att slutföra, beroende på klustermiljön.

Nu har du distribuerat konsuln till AKS-klustret. För att säkerställa att vi har en lyckad distribution av konsuln, låt oss gå vidare till nästa avsnitt för att validera consul installationen.

## <a name="validate-the-consul-installation"></a>Validera konsulinstallationen

Bekräfta att resurserna har skapats. Använd [kubectl get svc][kubectl-get] och [kubectl get][kubectl-get] `consul` pod-kommandon för att fråga namnområdet, där consul-komponenterna `helm install` installerades av kommandot:

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

Följande exempelutdata visar de tjänster och poddar (schemalagda på Linux-noder) som nu ska köras:

```output
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

Alla poddar ska visa statusen `Running`. Om dina poddar inte har dessa statusar, vänta en minut eller två tills de gör det. Om några poddar rapporterar ett problem använder du kommandot [kubectl describe pod][kubectl-describe] för att granska deras utdata och status.

## <a name="accessing-the-consul-ui"></a>Komma åt konsulgränssnittet

Consul UI installerades i vår installation ovan och ger UI-baserad konfiguration för consul. Användargränssnittet för konsuln exponeras inte offentligt via en extern IP-adress. Använd kommandot [kubectl port-forward][kubectl-port-forward] för att komma åt consul-användargränssnittet. Det här kommandot skapar en säker anslutning mellan klientdatorn och den relevanta podden i AKS-klustret.

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

Du kan nu öppna en `http://localhost:8080/ui` webbläsare och peka på den för att öppna konsulgränssnittet. Du bör se följande när du öppnar användargränssnittet:

![Consul UI](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Avinstallera konsul från AKS

> [!WARNING]
> Om du tar bort konsuln från ett system som körs kan det leda till trafikrelaterade problem mellan dina tjänster. Se till att du har gjort avsättningar för ditt system att fortfarande fungera korrekt utan konsul innan du fortsätter.

### <a name="remove-consul-components-and-namespace"></a>Ta bort consul-komponenter och namnområde

Om du vill ta bort konsuln från AKS-klustret använder du följande kommandon. Kommandona `helm delete` tar bort `consul` diagrammet och `kubectl delete namespace` kommandot tar `consul` bort namnområdet.

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du vill utforska fler installations- och konfigurationsalternativ för konsul finns i följande officiella konsulartiklar:

- [Konsul - Helm installationsguide][consul-install-k8]
- [Installationsalternativ för konsul - Helm][consul-install-helm-options]

Du kan också följa ytterligare scenarier med hjälp av:

- [Exempelprogram för konsul][consul-app-example]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://www.consul.io/docs/platform/k8s/run.html
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-app-example]: https://github.com/hashicorp/demo-consul-101/tree/master/k8s
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md

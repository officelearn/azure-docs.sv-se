---
title: Installera Hashicorp konsulär i Azure Kubernetes service (AKS)
description: Lär dig hur du installerar och använder konsulär för att skapa ett service nät i ett Azure Kubernetes service-kluster (AKS)
services: container-service
author: dstrebel
ms.service: container-service
ms.topic: article
ms.date: 06/19/2019
ms.author: dastrebe
ms.openlocfilehash: 7acd2533079499091427c7e63741b9c587ee29e5
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70189229"
---
# <a name="install-and-use-consul-connect-in-azure-kubernetes-service-aks"></a>Installera och Använd konsulär Connect i Azure Kubernetes service (AKS)

[Konsulär][consul-github] är ett nät med öppen källkod som ger en nyckel uppsättning över mikrotjänster i ett Kubernetes-kluster. Dessa funktioner omfattar tjänst identifiering, hälso kontroll, service segmentering och att det går att observera. Mer information om konsulär finns i den officiella artikeln [Vad är konsulär?][consul-docs-concepts] dokumentation.

Den här artikeln visar hur du installerar konsulär. Konsulära komponenter installeras i ett Kubernetes-kluster på AKS.

> [!NOTE]
> Dessa instruktioner hänvisar till konsulär `1.5`version.
>
> Konsulära `1.5.x` versioner har testats av Hashicorp-teamet mot Kubernetes- `1.12`versioner `1.14`, `1.14`,. Du hittar ytterligare konsulära versioner på [GitHub-uppdateringar][consul-github-releases] och information om var och en av de versioner som finns i [konsulära versions anteckningar][consul-release-notes].

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Installera konsulära komponenter på AKS
> * Verifiera den konsulära installationen
> * Avinstallera konsulär från AKS

## <a name="before-you-begin"></a>Innan du börjar

De steg som beskrivs i den här artikeln förutsätter att du har skapat ett AKS `1.11` -kluster (Kubernetes och senare, med RBAC aktiverat) `kubectl` och har upprättat en anslutning till klustret. Om du behöver hjälp med något av dessa objekt kan du se snabb starten för [AKS][aks-quickstart].

Du behöver [Helm][helm] för att följa dessa anvisningar och installera konsulär. Vi rekommenderar att du har version `2.12.2` eller senare installerat och konfigurerat i klustret på rätt sätt. Om du behöver hjälp med att installera Helm kan du läsa mer i [installations vägledningen för AKS Helm][helm-install]. Alla konsulära poddar måste också vara schemalagda att köras på Linux-noder.

Den här artikeln separerar den konsulära installations vägledningen i flera diskreta steg. Slut resultatet är detsamma i strukturen som den officiella konsulära installations [vägledningen][consul-install-k8].

### <a name="install-the-consul-components-on-aks"></a>Installera konsulära komponenter på AKS

Vi börjar med att klona den officiella HashiCorp konsulär om Kubernetes GitHub lagrings platsen.

```bash
git clone https://github.com/hashicorp/consul-helm.git
cd consul-helm
```

Vi behöver sedan skapa en anpassad Helm-fil för den konsulära installationen. Skapa följande anpassade värden-fil innan du installerar konsulär.

```bash
vim consul-values.yaml
```

Kopiera och klistra sedan in följande värden i filen konsulär-Values. yaml.

```yaml
# Sets datacenter name and version Consul to use
global:
  datacenter: dc1
  image: "consul:1.5.2"

# Enables proxies to be injected into pods
connectInject:
  enabled: true

# Enables UI on ClusterIP
ui:
  service:
    type: "ClusterIP"

# Enables GRCP that is required for connectInject
client:
  enabled: true
  grpc: true

# Sets replicase to 3 for HA
server:
  replicas: 3
  bootstrapExpect: 1
  disruptionBudget:
    enabled: true
    maxUnavailable: 1

# Syncs Kubernetes service discovery with Consul
syncCatalog:
  enabled: true
```

Nu när vi har en fil med anpassade värden kan vi installera konsulär i vårt AKS-kluster

Bash

```bash
helm install -f consul-values.yaml --name consul --namespace consul .
```
`Consul` Helm-diagrammet distribuerar ett antal objekt. Du kan se listan från utdata `helm install` från kommandot ovan. Det kan ta 4 till fem minuter att slutföra distributionen av konsulära komponenter, beroende på kluster miljön.

> [!NOTE]
> Alla konsulära poddar måste vara schemalagda att köras på Linux-noder. Om du har Windows Server-adresspooler utöver Linux-noder i klustret, kontrollerar du att alla konsulära poddar har schemalagts att köras på Linux-noder.

Nu har du distribuerat konsulär till ditt AKS-kluster. För att säkerställa att vi har en lyckad distribution av konsulär kan vi gå vidare till nästa avsnitt för att verifiera den konsulära installationen.

## <a name="validate-the-consul-installation"></a>Verifiera den konsulära installationen

Bekräfta först att de förväntade tjänsterna har skapats. Använd kommandot [kubectl get SVC][kubectl-get] för att visa de tjänster som körs. Fråga namn området där konsulära komponenter har installerats `consul` av Helm-diagrammet: `consul`

```console
kubectl get svc --namespace consul
```

Följande exempel på utdata visar de tjänster som nu ska köras:

```console
NAME                                 TYPE           CLUSTER-IP     EXTERNAL-IP             PORT(S)                                                                   AGE
consul                               ExternalName   <none>         consul.service.consul   <none>                                                                    38m
consul-consul-connect-injector-svc   ClusterIP      10.0.89.113    <none>                  443/TCP                                                                   40m
consul-consul-dns                    ClusterIP      10.0.166.82    <none>                  53/TCP,53/UDP                                                             40m
consul-consul-server                 ClusterIP      None           <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   40m
consul-consul-ui                     ClusterIP      10.0.117.164   <none>                  80/TCP                                                                    40m
```

Bekräfta sedan att nödvändiga poddar har skapats. Använd kommandot [kubectl get poddar][kubectl-get] och fråga `consul` namn området igen:

```console
kubectl get pods --namespace consul
```

Följande exempel på utdata visar de poddar som kör:

```console
NAME                                                              READY   STATUS    RESTARTS   AGE
consul-consul-7cc9v                                               1/1     Running   0          37m
consul-consul-7klg7                                               1/1     Running   0          37m
consul-consul-connect-injector-webhook-deployment-57f88df8hgmfs   1/1     Running   0          37m
consul-consul-lq8qb                                               1/1     Running   0          37m
consul-consul-server-0                                            1/1     Running   0          37m
consul-consul-server-1                                            1/1     Running   0          36m
consul-consul-server-2                                            1/1     Running   0          36m
consul-consul-sync-catalog-7cf7d5bfff-jjbjv                       1/1     Running   2          37m
```

 Alla poddar bör visa status för `Running`. Om din poddar inte har dessa status värden väntar du en minut eller två tills de gör det. Om en poddar rapporterar ett problem använder du kommandot [kubectl beskriver Pod][kubectl-describe] för att granska deras utdata och status.

## <a name="accessing-the-consul-ui"></a>Åtkomst till konsulärt användar gränssnitt

Konsulärt användar gränssnitt installerades i vår installation ovan som tillhandahåller UI-baserad konfiguration för konsulär. Användar gränssnittet för konsulär visas inte offentligt via en extern IP-adress. Använd kommandot [kubectl Port-Forward][kubectl-port-forward] för att få åtkomst till användar gränssnitten för tillägg. Det här kommandot skapar en säker anslutning mellan klient datorn och relevanta Pod i ditt AKS-kluster.

```bash
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```
Nu kan du öppna en webbläsare och peka den för `http://localhost:8080/ui` att öppna det konsulära användar gränssnittet. Du bör se följande när du öppnar användar gränssnittet:

![Konsulärt användar gränssnitt](./media/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Avinstallera konsulär från AKS

> [!WARNING]
> Om du tar bort konsulär från ett system som körs kan trafik problem uppstå mellan dina tjänster. Se till att du har gjort att systemet fortfarande fungerar korrekt utan konsulär innan du fortsätter.

### <a name="remove-consul-components-and-namespace"></a>Ta bort konsulära komponenter och namnrymd

Om du vill ta bort konsulär från AKS-klustret använder du följande kommandon. De `helm delete` här kommandona `consul` tar bort diagrammet och `kubectl delete ns` tar bort `consul` namn området.

```bash
helm delete --purge consul
kubectl delete ns consul
```

## <a name="next-steps"></a>Nästa steg

För att utforska fler installations-och konfigurations alternativ för konsulär, se följande officiella konsulära artiklar:

- [Konsulär-Helm installations guide][consul-install-k8]
- [Konsulär – Helm installations alternativ][consul-install-helm-options]

Du kan också följa ytterligare scenarier med hjälp av [konsulär exempel program][consul-app-example].

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

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md

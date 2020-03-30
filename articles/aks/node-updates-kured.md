---
title: Uppdatera och starta om Linux-noder med kured i Azure Kubernetes Service (AKS)
description: Lär dig hur du uppdaterar Linux-noder och automatiskt startar om dem med kured i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/28/2019
ms.openlocfilehash: 74b12c1bc6e2a88582cc357c8091b5590e6bf3cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191290"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Tillämpa säkerhets- och kernel-uppdateringar på Linux-noder i Azure Kubernetes Service (AKS)

För att skydda dina kluster tillämpas säkerhetsuppdateringar automatiskt på Linux-noder i AKS. Dessa uppdateringar inkluderar os-säkerhetskorrigeringar eller kärnuppdateringar. Vissa av dessa uppdateringar kräver en nod omstart för att slutföra processen. AKS startar inte automatiskt om dessa Linux-noder för att slutföra uppdateringsprocessen.

Processen för att hålla Windows Server-noder (för närvarande i förhandsversion i AKS) uppdaterade är lite annorlunda. Windows Server-noder får inte dagliga uppdateringar. I stället utför du en AKS-uppgradering som distribuerar nya noder med den senaste basfönstret Server-avbildningen och korrigeringsfilerna. För AKS-kluster som använder Windows Server-noder finns i [Uppgradera en nodpool i AKS][nodepool-upgrade].

Den här artikeln visar hur du använder kured med öppen källkod [(KUbernetes REboot Daemon)][kured] för att titta efter Linux-noder som kräver en omstart och sedan automatiskt hantera omplanering av att köra poddar och nodomstart.

> [!NOTE]
> `Kured`är ett open source-projekt av Weaveworks. Stöd till detta projekt i AKS ges på bästa sätt. Ytterligare support finns i slackkanalen #weave community.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster läser du SNABBSTARTen för AKS [med Azure CLI][aks-quickstart-cli] eller använder [Azure-portalen][aks-quickstart-portal].

Du behöver också Azure CLI version 2.0.59 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Förstå uppdateringsupplevelsen för AKS-nod

I ett AKS-kluster körs dina Kubernetes-noder som virtuella Azure-datorer (VIRTUELLA datorer). Dessa Linux-baserade virtuella datorer använder en Ubuntu-avbildning, med operativsystemet konfigurerat för att automatiskt söka efter uppdateringar varje kväll. Om säkerhets- eller kärnuppdateringar är tillgängliga hämtas och installeras de automatiskt.

![AKS-noduppdaterings- och omstartsprocess med kured](media/node-updates-kured/node-reboot-process.png)

Vissa säkerhetsuppdateringar, till exempel kernel-uppdateringar, kräver en omstart av nod för att slutföra processen. En Linux-nod som kräver en omstart skapar en fil med namnet */var/run/reboot-required*. Den här omstartsprocessen sker inte automatiskt.

Du kan använda dina egna arbetsflöden och processer för `kured` att hantera omstarter av nod, eller använda för att dirigera processen. Med `kured`distribueras en [DaemonSet][DaemonSet] som kör en pod på varje Linux-nod i klustret. Dessa poddar i DaemonSet-bevakningen för förekomsten av filen */var/run/reboot-required* och initiera sedan en process för att starta om noderna.

### <a name="node-upgrades"></a>Nod uppgraderingar

Det finns ytterligare en process i AKS som gör att du kan *uppgradera* ett kluster. En uppgradering är vanligtvis att flytta till en nyare version av Kubernetes, inte bara tillämpa nod säkerhetsuppdateringar. En AKS-uppgradering utför följande åtgärder:

* En ny nod distribueras med de senaste säkerhetsuppdateringarna och Kubernetes-versionen tillämpas.
* En gammal nod är avspärrad och dränerad.
* Poddar är schemalagda på den nya noden.
* Den gamla noden tas bort.

Du kan inte finnas kvar på samma Kubernetes-version under en uppgraderingshändelse. Du måste ange en nyare version av Kubernetes. Om du vill uppgradera till den senaste versionen av Kubernetes kan du [uppgradera AKS-klustret][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Distribuera kured i ett AKS-kluster

Om du `kured` vill distribuera DaemonSet installerar du följande officiella Kured Helm-diagram. Detta skapar en roll och klusterroll, bindningar och ett tjänstkonto och distribuerar sedan DaemonSet med `kured`.

```console
# Add the stable Helm repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Update your local Helm chart repository cache
helm repo update

# Create a dedicated namespace where you would like to deploy kured into
kubectl create namespace kured

# Install kured in that namespace with Helm 3 (only on Linux nodes, kured is not working on Windows nodes)
helm install kured stable/kured --namespace kured --set nodeSelector."beta\.kubernetes\.io/os"=linux
```

Du kan också konfigurera `kured`ytterligare parametrar för , till exempel integrering med Prometheus eller Slack. Mer information om ytterligare konfigurationsparametrar finns i det [kurerade Helm-diagrammet][kured-install].

## <a name="update-cluster-nodes"></a>Uppdatera klusternoder

Som standard söker Linux-noder i AKS efter uppdateringar varje kväll. Om du inte vill vänta kan du utföra en uppdatering `kured` manuellt för att kontrollera att den körs korrekt. Följ först stegen till [SSH till en av dina AKS-noder][aks-ssh]. När du har en SSH-anslutning till Linux-noden, kontrollera om uppdateringar och tillämpa dem enligt följande:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Om uppdateringar har tillämpats som kräver en omstart av noden skrivs en fil till */var/run/reboot-required*. `Kured`söker efter noder som kräver en omstart var 60:e minut som standard.

## <a name="monitor-and-review-reboot-process"></a>Övervaka och granska omstartsprocessen

När en av replikerna i DaemonSet har upptäckt att en omstart av noden krävs placeras ett lås på noden via Kubernetes API. Det här låset förhindrar att ytterligare poddar schemaläggs på noden. Låset anger också att endast en nod ska startas om åt gången. När noden är avspärrad töms löpkapslar från noden och noden startas om.

Du kan övervaka status för noderna med kommandot [kubectl get noder.][kubectl-get-nodes] Följande exempelutdata visar en nod med *statusen SchedulingDisabled* när noden förbereder omstartsprocessen:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

När uppdateringsprocessen är klar kan du visa status för noderna med kommandot `--output wide` [kubectl get noder][kubectl-get-nodes] med parametern. Med den här ytterligare utdata kan du se en skillnad i *KERNEL-VERSION* av de underliggande noderna, vilket visas i följande exempelutdata. *Aks-nodepool1-28993262-0* uppdaterades i ett tidigare steg och visar kernel version *4.15.0-1039-azure*. Noden *aks-nodepool1-28993262-1* som inte har uppdaterats visar kernel version *4.15.0-1037-azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Nästa steg

I den här `kured` artikeln beskrivs hur du använder för att starta om Linux-noder automatiskt som en del av säkerhetsuppdateringsprocessen. Om du vill uppgradera till den senaste versionen av Kubernetes kan du [uppgradera AKS-klustret][aks-upgrade].

För AKS-kluster som använder Windows Server-noder finns i [Uppgradera en nodpool i AKS][nodepool-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://hub.helm.sh/charts/stable/kured
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool

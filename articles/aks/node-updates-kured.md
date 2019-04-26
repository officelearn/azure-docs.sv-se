---
title: Uppdatera och starta om noder med kured i Azure Kubernetes Service (AKS)
description: Lär dig hur du uppdaterar noder och automatiskt starta om dem med kured i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: 75057f6bd92fbdc805da2e0e36dc2bff7b069f26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60465004"
---
# <a name="apply-security-and-kernel-updates-to-nodes-in-azure-kubernetes-service-aks"></a>Gäller säkerhet och kernel-uppdateringar för noder i Azure Kubernetes Service (AKS)

För att skydda dina kluster kan tillämpas automatiskt säkerhetsuppdateringar till noderna i AKS. Dessa uppdateringar innehåller OS säkerhetskorrigeringar eller kernel-uppdateringar. Vissa av dessa uppdateringar kräver en nod startas om för att slutföra processen. AKS inte automatiskt starta om noderna för att slutföra uppdateringsprocessen.

Den här artikeln visar hur du använder öppen källkod [kured (KUbernetes starta om Daemon)] [ kured] kan du titta på för noder som kräver en omstart, sedan automatiskt hantera den ändra på tidsplanering för att köra poddar och omstart av nod processen.

> [!NOTE]
> `Kured` är ett projekt med öppen källkod av Weaveworks. Stöd för det här projektet i AKS tillhandahålls i mån av möjlighet. Ytterligare support finns i slack kanal #weave-community

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster finns i snabbstarten om AKS [med Azure CLI] [ aks-quickstart-cli] eller [med Azure portal][aks-quickstart-portal].

Du också ha Azure CLI version 2.0.59 eller senare installerat och konfigurerat. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Förstå av uppdateringar för AKS-nod

I ett AKS-kluster kör Kubernetes-noderna som virtuella Azure-datorer (VM). Dessa Linux-baserade virtuella datorer använder en Ubuntu-avbildning med OS som konfigurerats för att automatiskt söka efter uppdateringar varje natt. Om säkerhets- eller kernel-uppdateringar är tillgängliga kan de automatiskt hämtas och installeras.

![AKS noden uppdatera och starta om processen med kured](media/node-updates-kured/node-reboot-process.png)

Vissa säkerhetsuppdateringar, till exempel kernel-uppdateringar kräver en nod startas om för att slutföra processen. En nod som kräver en omstart skapar en fil med namnet */var/run/reboot-required*. Den här processen för omstart sker inte automatiskt.

Du kan använda din egen arbetsflöden och processer för att hantera omstarter av noden, eller använda `kured` att dirigera processen. Med `kured`, ett [DaemonSet] [ DaemonSet] distribueras som körs en pod på varje nod i klustret. Dessa poddar i DaemonSet Håll utkik efter förekomsten av den */var/run/reboot-required* fil och sedan startar en process för att starta om noderna.

### <a name="node-upgrades"></a>Uppgraderingen

Det finns en till process i AKS som låter dig *uppgradera* ett kluster. En uppgradering är vanligtvis att flytta till en nyare version av Kubernetes, inte bara tillämpa säkerhetsuppdateringar för noden. Ett AKS-uppgraderingen utförs följande åtgärder:

* En ny nod distribueras med de senaste säkerhetsuppdateringarna och Kubernetes-version som används.
* En gammal nod avspärrade och tömda.
* Poddar schemaläggs på den nya noden.
* Den gamla noden tas bort.

Du kan inte finnas kvar på samma Kubernetes-version under en uppgradering händelse. Du måste ange en nyare version av Kubernetes. Om du vill uppgradera till den senaste versionen av Kubernetes, kan du [uppgradera AKS-klustret][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Distribuera kured i ett AKS-kluster

Att distribuera den `kured` DaemonSet, Använd följande exempel YAML manifest från sina GitHub-projektsidan. Den här manifestet skapar en roll och klusterrollen bindningar och ett tjänstkonto och sedan distribuerar den DaemonSet med hjälp av `kured` version 1.1.0 som stöder AKS-kluster 1.9 eller senare.

```console
kubectl apply -f https://github.com/weaveworks/kured/releases/download/1.1.0/kured-1.1.0.yaml
```

Du kan också konfigurera ytterligare parametrar för `kured`, till exempel integration med Prometheus eller Slack. Mer information om ytterligare konfigurationsparametrar finns i den [kured installation docs][kured-install].

## <a name="update-cluster-nodes"></a>Uppdatera klusternoder

AKS-noder Sök efter uppdateringar varje kväll som standard. Om du inte vill vänta kan du manuellt utföra en uppdatering för att kontrollera att `kured` körs korrekt. Följ stegen för att först [SSH till någon av AKS-noder][aks-ssh]. När du har en SSH-anslutning till noden kan söka efter uppdateringar och tillämpa dem på följande sätt:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Om uppdateringar har tillämpats som kräver en omstart av nod, en fil skrivs till */var/run/reboot-required*. `Kured` söker efter noder som kräver en omstart var 60: e minut som standard.

## <a name="monitor-and-review-reboot-process"></a>Övervaka och granskningsprocessen för omstart

När en av replikerna i DaemonSet har upptäckt att det krävs en omstart av nod, Lås ett för noden via API: et för Kubernetes. Den här låset förhindrar ytterligare poddar som schemaläggs på noden. Låset anger också att endast en nod ska startas om samtidigt. Med noden avspärrade körs poddar är tömda från noden och noden startas om.

Du kan övervaka statusen för de noder som använder den [kubectl få noder] [ kubectl-get-nodes] kommando. Följande Exempelutdata visar en nod med statusen *SchedulingDisabled* eftersom noden förbereder för att starta om:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

När uppdateringen är klar kan du visa status för de noder som använder den [kubectl få noder] [ kubectl-get-nodes] med den `--output wide` parametern. Dessa ytterligare utdata kan du se en skillnad i *KERNEL-VERSION* av de underliggande noderna, som visas i följande Exempelutdata. Den *aks-nodepool1-28993262-0* uppdaterades i ett föregående steg och visar kernel-version *4.15.0-1039-azure*. Noden *aks-nodepool1-28993262-1* som inte har uppdaterat visar kernel-version *4.15.0-1037-azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs hur du använder `kured` att starta om noderna automatiskt som en del av processen för att uppdatera din. Om du vill uppgradera till den senaste versionen av Kubernetes, kan du [uppgradera AKS-klustret][aks-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured#installation
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md

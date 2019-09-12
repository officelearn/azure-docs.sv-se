---
title: Koncept – skala program i Azure Kubernetes Services (AKS)
description: Lär dig mer om skalning i Azure Kubernetes service (AKS), inklusive horisontell Pod-autoskalning, kluster autoskalning och Azure Container Instances anslutningen.
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: zarhoads
ms.openlocfilehash: 4fc34ed5cdd53977aa20bef84200ba2bf5386979
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899479"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Skalnings alternativ för program i Azure Kubernetes service (AKS)

När du kör program i Azure Kubernetes service (AKS) kan du behöva öka eller minska mängden beräknings resurser. Antalet underliggande Kubernetes-noder kan också behöva ändras som antalet program instanser som du behöver ändra. Du kan också behöva etablera ett stort antal ytterligare program instanser.

Den här artikeln beskriver de viktigaste begreppen som hjälper dig att skala program i AKS:

- [Skala manuellt](#manually-scale-pods-or-nodes)
- [Horisontell Pod autoskalning (HPA)](#horizontal-pod-autoscaler)
- [Kluster autoskalning](#cluster-autoscaler)
- [Azure Container instance-integrering (ACI) med AKS](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Skala poddar eller noder manuellt

Du kan skala repliker (poddar) och noder manuellt för att testa hur programmet svarar på en ändring i tillgängliga resurser och tillstånd. Genom att skala resurser manuellt kan du också definiera en uppsättning resurser som ska användas för att upprätthålla en fast kostnad, till exempel antalet noder. Om du vill skala manuellt definierar du repliken eller antalet noder och Kubernetes API-scheman skapar ytterligare poddar eller tömmer noder.

För att komma igång med manuell skalning av poddar och noder, se [skala program i AKS][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>Horisontell Pod autoskalning

Kubernetes använder den vågräta Pod autoskalning (HPA) för att övervaka resurs behovet och skalar automatiskt antalet repliker. Som standard kontrollerar autoskalning i horisontella Pod mått-API var 30: e sekund för alla nödvändiga ändringar i replik antalet. När ändringar krävs ökas eller minskas antalet repliker i enlighet med detta. Horisontell Pod autoskalning fungerar med AKS-kluster som har distribuerat mått servern för Kubernetes 1.8 +.

![Kubernetes horisontell Pod autoskalning](media/concepts-scale/horizontal-pod-autoscaling.png)

När du konfigurerar den horisontella Pod autoskalning för en specifik distribution definierar du det lägsta och högsta antalet repliker som kan köras. Du definierar också måttet för att övervaka och basera eventuella skalnings beslut, till exempel processor användning.

För att komma igång med den vågräta Pod-autoskalning i AKS, se [AutoScale-poddar i AKS][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Cooldown för skalnings händelser

I takt med att den vågräta Pod-autoskalning kontrollerar Metrics API var 30: e sekund kanske tidigare skalnings händelser inte har slutförts innan en annan kontroll görs. Detta kan orsaka att den vågräta Pod automatiskt skalar för att ändra antalet repliker innan föregående skalnings händelse har kunnat ta emot program arbets belastning och resurs behoven för att justera detta.

För att minimera dessa tävlings händelser anges cooldown eller fördröjnings värden. Dessa värden definierar hur länge den automatiska skalningen av vågrät Pod måste vänta efter en skalnings händelse innan en annan skalnings händelse kan utlösas. Det här beteendet gör att det nya replik antalet börjar gälla och att mått-API: et återspeglar den distribuerade arbets belastningen. Som standard är fördröjningen för skala upp händelser 3 minuter och fördröjningen för att skala ned händelser är 5 minuter

För närvarande kan du inte finjustera dessa cooldown-värden från standardvärdet.

## <a name="cluster-autoscaler"></a>Kluster autoskalning

För att svara på ändring av Pod-krav har Kubernetes en kluster autoskalning (för närvarande i för hands version i AKS) som justerar antalet noder baserat på de begärda beräknings resurserna i Node-poolen. Som standard kontrollerar kluster autoskalning den metriska API-servern var 10: e sekund för alla nödvändiga ändringar i antalet noder. Om klustrets autoskalning avgör att en ändring krävs, ökar eller minskar du antalet noder i ditt AKS-kluster. Kluster autoskalning fungerar med RBAC-aktiverade AKS-kluster som kör Kubernetes 1.10. x eller senare.

![Kubernetes-kluster autoskalning](media/concepts-scale/cluster-autoscaler.png)

Kluster autoskalning används vanligt vis tillsammans med den horisontella Pod autoskalning. När den vågräta Pod används ökas eller minskas antalet poddar baserat på program efter frågan och klustrets autoskalning justerar antalet noder som behövs för att köra ytterligare poddar.

Kluster autoskalning ska endast testas i för hands versionen i AKS-kluster.

För att komma igång med klustrets autoskalning i AKS, se [kluster autoskalning på AKS][aks-cluster-autoscaler].

### <a name="scale-up-events"></a>Skala upp händelser

Om en nod inte har tillräckligt med beräknings resurser för att köra en begärd pod, kan Pod inte passera genom schemaläggnings processen. Pod kan inte startas om inte ytterligare beräknings resurser är tillgängliga i Node-poolen.

När poddar för kluster autoskalning inte kan schemaläggas på grund av resurs begränsningar för en resurspool, ökar antalet noder i noden för att tillhandahålla ytterligare beräknings resurser. När de ytterligare noderna har distribuerats och är tillgängliga för användning i Node-poolen, schemaläggs poddar sedan att köras på dem.

Om ditt program behöver skala snabbt kan vissa poddar finnas kvar i ett tillstånd som väntar på att schemaläggas tills de ytterligare noder som distribueras av klustret autoskalning kan godkänna den schemalagda poddar. För program som har höga burst-krav kan du skala med virtuella noder och Azure Container Instances.

### <a name="scale-down-events"></a>Skala ned händelser

Klustrets autoskalning övervakar också Pod schemaläggnings status för noder som inte nyligen har fått nya tids planerings begär Anden. Det här scenariot indikerar att Node-poolen har fler beräknings resurser än vad som krävs och att antalet noder kan minskas.

En nod som skickar ett tröskelvärde som inte längre behövs i 10 minuter är schemalagd för borttagning. När den här situationen inträffar är poddar schemalagda att köras på andra noder i Node-poolen och klustrets autoskalning minskar antalet noder.

Dina program kan drabbas av vissa avbrott när poddar är schemalagda för olika noder när den automatiska skalnings tjänsten för klustret minskar antalet noder. Undvik avbrott genom att undvika program som använder en enda Pod-instans.

## <a name="burst-to-azure-container-instances"></a>Burst till Azure Container Instances

Om du snabbt vill skala ditt AKS-kluster kan du integrera med Azure Container Instances (ACI). Kubernetes har inbyggda komponenter för skalning av antalet repliker och noder. Men om ditt program behöver skala snabbt, kan den horisontella Pod autoskalning schemalägga mer poddar än vad som finns i de befintliga beräknings resurserna i Node-poolen. Om det här scenariot har kon figurer ATS utlöses det här scenariot för att distribuera ytterligare noder i Node-poolen, men det kan ta några minuter för noderna att etablera och tillåta Kubernetes Scheduler att köra poddar på dem.

![Kubernetes burst-skalning till ACI](media/concepts-scale/burst-scaling.png)

Med ACI kan du snabbt distribuera behållar instanser utan ytterligare infrastruktur belastning. När du ansluter med AKS blir ACI ett säkert, logiskt tillägg för ditt AKS-kluster. Den virtuella Kubelet-komponenten installeras i ditt AKS-kluster som visar ACI som en virtuell Kubernetes-nod. Kubernetes kan sedan schemalägga poddar som körs som ACI-instanser via virtuella noder, inte som poddar på VM-noder direkt i ditt AKS-kluster. Virtuella noder är för närvarande för hands versioner i AKS.

Programmet kräver ingen ändring för att använda virtuella noder. Distributioner kan skalas över AKS och ACI och utan fördröjning som kluster autoskalning distribuerar nya noder i ditt AKS-kluster.

Virtuella noder distribueras till ett extra undernät i samma virtuella nätverk som ditt AKS-kluster. Med den här virtuella nätverks konfigurationen kan trafiken mellan ACI och AKS skyddas. Precis som ett AKS-kluster är en ACI-instans en säker, logisk beräknings resurs som är isolerad från andra användare.

## <a name="next-steps"></a>Nästa steg

För att komma igång med skalnings program, följ först [snabb starten för att skapa ett AKS-kluster med Azure CLI][aks-quickstart]. Du kan sedan starta manuellt eller automatiskt skala program i ditt AKS-kluster:

- Skala [poddar][aks-manually-scale-pods] eller [noder][aks-manually-scale-nodes] manuellt
- Använd den [vågräta Pod autoskalning][aks-hpa]
- Använd [kluster autoskalning][aks-cluster-autoscaler]

Mer information om kärn Kubernetes-och AKS-koncept finns i följande artiklar:

- [Kubernetes/AKS-kluster och arbets belastningar][aks-concepts-clusters-workloads]
- [Kubernetes/AKS-åtkomst och identitet][aks-concepts-identity]
- [Kubernetes/AKS-säkerhet][aks-concepts-security]
- [Kubernetes/AKS virtuella nätverk][aks-concepts-network]
- [Kubernetes/AKS-lagring][aks-concepts-storage]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md

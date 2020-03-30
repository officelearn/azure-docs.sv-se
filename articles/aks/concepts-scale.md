---
title: Begrepp - Skala program i Azure Kubernetes Services (AKS)
description: Lär dig mer om skalning i Azure Kubernetes Service (AKS), inklusive vågrätt automatisk skalning av pod, automatisk skalning av kluster och Azure Container Instances-kopplingen.
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: zarhoads
ms.openlocfilehash: 396e5bc31723768ada334dd5043bca724af5e84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595866"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Skalningsalternativ för program i Azure Kubernetes Service

När du kör program i Azure Kubernetes Service (AKS) kan du behöva öka eller minska mängden beräkningsresurser. När antalet programinstanser du behöver ändras kan antalet underliggande Kubernetes-noder också behöva ändras. Du kan också behöva snabbt etablera ett stort antal ytterligare programinstanser.

I den här artikeln beskrivs de grundläggande begreppen som hjälper dig att skala program i AKS:

- [Skala manuellt](#manually-scale-pods-or-nodes)
- [Automatisk skalningsapparat för vågrät kapsel (HPA)](#horizontal-pod-autoscaler)
- [Automatisk skalning av kluster](#cluster-autoscaler)
- [ACI-integrering (Azure Container Instance) med AKS](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Skala poddar eller noder manuellt

Du kan manuellt skala repliker (poddar) och noder för att testa hur ditt program svarar på en ändring av tillgängliga resurser och tillstånd. Med manuellt skalning av resurser kan du också definiera en viss mängd resurser som ska användas för att underhålla en fast kostnad, till exempel antalet noder. Om du vill skala manuellt definierar du antalet repliker eller nod. Kubernetes API schemalägger sedan skapa ytterligare poddar eller tömma noder baserat på att replik eller nod räknas.

När du skalar ned noder anropar Kubernetes API relevant Azure Compute API som är kopplat till den beräkningstyp som används av klustret. Till exempel för kluster som bygger på VM-skala Anger logiken för att välja vilka noder som ska tas bort bestäms av API:et för vm-skalningsuppsättningar. Mer information om hur noder väljs för borttagning på skala ned finns i [vanliga frågor och svar om VMSS](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed).

För att komma igång med manuellt skalning poddar och noder se [Skala program i AKS][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>Automatisk skalningsapparat för vågrät pod

Kubernetes använder den horisontella pod autoscaler (HPA) för att övervaka resursbehovet och automatiskt skala antalet repliker. Som standard kontrollerar den automatiska pod-pod-pod-skalningen metrisk-API:et var 30:e sekund efter alla nödvändiga ändringar i antalet repliker. När ändringar krävs ökas eller minskas antalet repliker i enlighet med detta. Den automatiska bildrensaren för vågrät pod fungerar med AKS-kluster som har distribuerat Metrics Server för Kubernetes 1.8+.

![Kubernetes horisontella pod automatiskskalering](media/concepts-scale/horizontal-pod-autoscaling.png)

När du konfigurerar den automatiska vågen pod-pod-skalningen för en viss distribution definierar du det minsta och högsta antalet repliker som kan köras. Du definierar också måttet för att övervaka och basera eventuella skalningsbeslut på, till exempel CPU-användning.

Mer om du vill komma igång med den horisontella pod-förrättaren i AKS finns [i Poddar för automatisk skalning i AKS][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Cooldown av skalning händelser

När den horisontella pod-versionen kontrollerar mått-API:et var 30:e sekund kanske tidigare skalningshändelser inte har slutförts innan en ny kontroll görs. Detta kan orsaka att den vågräta pod-versionen ändrar antalet repliker innan den föregående skalningshändelsen kan ta emot programarbetsbelastning och resurskraven justeras därefter.

För att minimera dessa race händelser, cooldown eller fördröjning värden är inställda. Dessa värden definierar hur länge den automatiska pod-pod-pod-skalan måste vänta efter en skalningshändelse innan en annan skalningshändelse kan utlösas. Med det här beteendet kan det nya replikantalet börja gälla och mått-API:et för att återspegla den distribuerade arbetsbelastningen. Som standard är fördröjningen på uppskalningshändelser 3 minuter och fördröjningen på nedskalningshändelser är 5 minuter

För närvarande kan du inte justera dessa cooldown-värden från standardvärdet.

## <a name="cluster-autoscaler"></a>Automatisk skalning av kluster

För att svara på ändrade podskrav har Kubernetes en automatisk skalning av kluster, som justerar antalet noder baserat på de begärda beräkningsresurserna i nodpoolen. Som standard kontrollerar kluster automatisk skalning API-servern var 10:e sekund efter alla nödvändiga ändringar i antal noder. Om automatisk skalning av klustret avgör att en ändring krävs, ökas eller minskas antalet noder i AKS-klustret. Klusterautomatiska skalning fungerar med RBAC-aktiverade AKS-kluster som kör Kubernetes 1.10.x eller senare.

![Kubernetes kluster automatisk skalning](media/concepts-scale/cluster-autoscaler.png)

Automatisk skalning av kluster används vanligtvis tillsammans med den automatiska vågen pod-skalningen. När den kombineras ökar eller minskar den horisontella pod-för-enheten antalet poddar baserat på programmets behov, och klusterautomatenheten justerar antalet noder efter behov för att köra dessa ytterligare poddar därefter.

Mer om du vill komma igång med klusterautomaten i AKS finns i [Automatisk klusterskalning på AKS][aks-cluster-autoscaler].

### <a name="scale-up-events"></a>Skala upp händelser

Om en nod inte har tillräckliga beräkningsresurser för att köra en begärd pod, kan den podden inte gå vidare genom schemaläggningsprocessen. Podden kan inte starta om inte ytterligare beräkningsresurser är tillgängliga i nodpoolen.

När klusterautomaten märker poddar som inte kan schemaläggas på grund av begränsningar för nodpoolresurs, ökas antalet noder i nodpoolen för att tillhandahålla ytterligare beräkningsresurser. När dessa ytterligare noder har distribuerats och är tillgängliga för användning i nodpoolen, schemaläggs poddar sedan att köras på dem.

Om ditt program behöver skalas snabbt kan vissa poddar finnas kvar i ett tillstånd som väntar på att schemaläggas tills de ytterligare noder som distribueras av klusterautomatenheten kan acceptera de schemalagda poddarna. För program som har höga burst-krav kan du skala med virtuella noder och Azure Container Instances.

### <a name="scale-down-events"></a>Skala ner händelser

Klusterautomatskalningen övervakar också pod-schemaläggningsstatusen för noder som inte nyligen har fått nya schemaläggningsbegäranden. Det här scenariot anger att nodpoolen har fler beräkningsresurser än vad som krävs och antalet noder kan minskas.

En nod som skickar ett tröskelvärde för att inte längre behövas i 10 minuter som standard schemaläggs för borttagning. När den här situationen inträffar schemaläggs poddar att köras på andra noder i nodpoolen och klusterautomatskalningen minskar antalet noder.

Dina program kan uppstå vissa störningar eftersom poddar schemaläggs på olika noder när kluster automatiskskalning minskar antalet noder. Undvik program som använder en enda pod-instans för att minimera störningar.

## <a name="burst-to-azure-container-instances"></a>Burst till Azure Container instanser

Om du snabbt vill skala aks-klustret kan du integrera med Azure Container Instances (ACI). Kubernetes har inbyggda komponenter för att skala replik- och nodantalet. Men om ditt program behöver snabbt skalas, kan den horisontella pod autoskaleraren schemalägga fler poddar än vad som kan tillhandahållas av de befintliga beräkningsresurserna i nodpoolen. Om det här scenariot är konfigurerat utlöses den automatiska skalaren för kluster för att distribuera ytterligare noder i nodpoolen, men det kan ta några minuter för dessa noder att etableras och låta Kubernetes-schemaläggaren köra poddar på dem.

![Kubernetes burst skalning till ACI](media/concepts-scale/burst-scaling.png)

Med ACI kan du snabbt distribuera behållarinstanser utan ytterligare infrastrukturkostnader. När du ansluter till AKS blir ACI ett säkert, logiskt tillägg av AKS-klustret. Komponenten [virtuella noder,][virtual-nodes-cli] som baseras på [Virtual Kubelet,][virtual-kubelet]installeras i AKS-klustret som presenterar ACI som en virtuell Kubernetes-nod. Kubernetes kan sedan schemalägga poddar som körs som ACI-instanser via virtuella noder, inte som poddar på VM-noder direkt i AKS-klustret. Virtuella noder förhandsgranskas för närvarande i AKS.

Programmet kräver ingen ändring för att använda virtuella noder. Distributioner kan skalas över AKS och ACI och utan fördröjning som kluster automatisk skalning distribuerar nya noder i AKS-klustret.

Virtuella noder distribueras till ytterligare ett undernät i samma virtuella nätverk som AKS-klustret. Med den här virtuella nätverkskonfigurationen kan trafiken mellan ACI och AKS skyddas. Precis som ett AKS-kluster är en ACI-instans en säker, logisk beräkningsresurs som är isolerad från andra användare.

## <a name="next-steps"></a>Nästa steg

För att komma igång med skalningsprogram, följ först [snabbstarten för att skapa ett AKS-kluster med Azure CLI][aks-quickstart]. Du kan sedan börja skala program manuellt eller automatiskt i AKS-klustret:

- Skala [poddar][aks-manually-scale-pods] eller [noder][aks-manually-scale-nodes] manuellt
- Använd den [horisontella pod autoscaler][aks-hpa]
- Använda [kluster automatisk skalning][aks-cluster-autoscaler]

Mer information om kubernetes och AKS-koncept finns i följande artiklar:

- [Kubernetes / AKS kluster och arbetsbelastningar][aks-concepts-clusters-workloads]
- [Kubernetes / AKS tillgång och identitet][aks-concepts-identity]
- [Kubernetes / AKS säkerhet][aks-concepts-security]
- [Kubernetes / AKS virtuella nätverk][aks-concepts-network]
- [Kubernetes / AKS lagring][aks-concepts-storage]

<!-- LINKS - external -->
[virtual-kubelet]: https://virtual-kubelet.io/

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
[virtual-nodes-cli]: virtual-nodes-cli.md
---
title: Begrepp - skala program i Azure Kubernetes Services (AKS)
description: Mer information om skalning i Azure Kubernetes Service (AKS), inklusive vågrät pod autoskalningen, kluster autoskalningen och Azure Container Instances-anslutningen.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: 7cd6af1dc6b947abd91c9d25a4b102e52da9d24d
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56456732"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Skalningsalternativ för program i Azure Kubernetes Service (AKS)

När du kör program i Azure Kubernetes Service (AKS), kan du behöva öka eller minska mängden beräkningsresurser. När antalet instanser av programmet måste du ändra antalet underliggande Kubernetes noder kan också behöva ändra. Du kan också behöva etablera snabbt ett stort antal ytterligare programinstanser.

Den här artikeln beskrivs grundläggande begrepp som hjälper dig att skala program i AKS:

- [Skala manuellt](#manually-scale-pods-or-nodes)
- [Vågrät pod autoskalningen (HPA)](#horizontal-pod-autoscaler)
- [Klustret autoskalningen](#cluster-autoscaler)
- [Azure Container-instans (ACI)-integrering med AKS](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Skala poddar eller noder manuellt

Du kan manuellt skala repliker (poddar) och noder för att testa hur ditt program svarar på en ändring i tillgängliga resurser och tillstånd. Skala resurser manuellt kan du definiera en viss mängd resurser som ska användas för att underhålla en fast kostnad, till exempel hur många noder. Om du vill skala manuellt definiera repliken eller antalet noder och Kubernetes API-scheman skapar nya poddarna eller tömmer noderna.

Du kommer igång med manuellt skala poddar och noder [skala program i AKS][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>Vågrät pod autoskalningen

Kubernetes använder vågrät pod autoskalningen (HPA) för att övervaka resursbehov och automatiskt skala antalet repliker. Som standard kontrollerar vågrät pod autoskalningen mått-API med 30 sekunders mellanrum för eventuella nödvändiga ändringar i replikantal. När ändringar krävs, antalet repliker ökar eller minskar därefter. Vågrät pod autoskalningen fungerar med AKS-kluster som har distribuerat mått-Server för Kubernetes 1.8 +.

![Vågrät Kubernetes pod automatisk skalning](media/concepts-scale/horizontal-pod-autoscaling.png)

När du konfigurerar vågrät pod autoskalningen för en viss distribution kan definiera du minsta och största antalet repliker som kan köras. Du kan också definiera mått för att övervaka och basera alla beslut om skalning, till exempel CPU-användning.

Kom igång med vågrät pod autoskalningen i AKS, se [autoskalning av poddarna i AKS][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Nedkylningstiden skala händelser

Eftersom vågrät pod autoskalningen kontrollerar mått-API med 30 sekunders mellanrum, kanske tidigare skalningshändelser inte har slutförts innan en annan kontroll görs. Det här beteendet kan orsaka vågräta pod autoskalningen ändra antalet repliker innan den föregående skalhändelse har kunnat ta emot arbetsbelastning och resurskrav att justeras.

Nedkylningstiden eller fördröjning värden kan anges för att minimera händelserna som ”vinner”. Dessa värden definierar hur länge vågrät pod autoskalningen måste vänta efter en skalhändelse innan en annan skala händelsen kan utlösas. Det här innebär att den nya repliken räkna träder i kraft och mått-API: et omfattar distribuerad arbetsbelastning. Som standard fördröjningen på skala upp händelser är 3 minuter och fördröjningen på skala ned händelser är 5 minuter

Du kan behöva justera dessa nedkylningstiden-värden. Standardvärden för nedkylningstiden kan ge intryck av att vågrät pod autoskalningen inte skalning replikantalet tillräckligt snabbt. Till exempel för att snabbt öka antalet repliker används kan minska den `--horizontal-pod-autoscaler-upscale-delay` när du skapar din vågrät pod autoskalningen definitioner med `kubectl`.

## <a name="cluster-autoscaler"></a>Klustret autoskalningen

För att svara på förändrade behov pod, har Kubernetes en kluster-autoskalningen som justerar antalet noder baserat på den begärda beräkningsresurser i noden poolen. Som standard kontrollerar autoskalningen kluster API-servern var tionde sekund för alla nödvändiga ändringar i antalet noder. Om klustret automatisk skalning krävs en ändring, antalet noder i AKS-kluster ökar eller minskar därefter. Autoskalningen kluster fungerar med RBAC-aktiverade AKS-kluster som kör Kubernetes 1.10.x eller högre.

![Kubernetes-kluster autoskalningen](media/concepts-scale/cluster-autoscaler.png)

Klustret autoskalningen används vanligen tillsammans med vågrät pod autoskalningen. I kombination, vågrät pod autoskalningen ökar eller minskar antalet poddar baserat på programmets efterfrågan och klustret autoskalningen justerar antalet noder som behövs för att köra de nya poddarna i enlighet med detta.

Kom igång med autoskalningen kluster i AKS, se [kluster Autoskalningen på AKS][aks-cluster-autoscaler].

### <a name="scale-up-events"></a>Skala upp händelser

Om en nod inte har tillräckligt med beräkningsresurser att köra en begärda pod, det går inte att den pod fortsätter genom planeringsprocessen. Poden kan inte starta om inte fler beräkningsresurser är tillgängliga i noden poolen.

När klustret autoskalningen meddelanden poddar som inte går att schemalägga på grund av resursbegränsningar för noden pool, ökas antalet noder i poolen för noden för att ge ytterligare beräkningsresurser. När dessa ytterligare noder är har distribuerats och är tillgänglig för användning i nodpoolen, är sedan poddarna schemalagda att köras på dem.

Om programmet behöver för att skala snabbt, kan vissa poddar kvar i ett tillstånd som väntar på att schemaläggas tills de ytterligare noder som distribueras av klustret autoskalningen kan acceptera schemalagda poddarna. För program som har hög burst-krav, kan du skala med virtuella noder och Azure Container Instances.

### <a name="scale-down-events"></a>Skala ned händelser

Autoskalningen klustret övervakar även pod schemaläggning status för noder som inte nyligen har tagit emot nya schemaläggning begäranden. Det här scenariot anger att nodpoolen har fler beräkningsresurser än vad som krävs och antalet noder kan minskas.

En nod som skickar ett tröskelvärde för längre som behövs i 10 minuter som standard har schemalagts för borttagning. När den här situationen uppstår poddar är schemalagda att köras på andra noder i poolen noden och klustret autoskalningen minskar antalet noder.

Vissa avbrott kan uppstå i dina program som poddar schemaläggs på olika noder när klustret autoskalningen minskar antalet noder. Undvik att program som använder en enda pod-instans för att minimera störningar.

## <a name="burst-to-azure-container-instances"></a>Utöka till Azure Container Instances

Snabbt skalar ditt AKS-kluster kan integrera du med Azure Container Instances (ACI). Kubernetes har inbyggda komponenter att skala antalet replik och nod. Om programmet behöver för att skala snabbt, kan vågrät pod autoskalningen schemalägga flera poddar än vad som kan anges av de befintliga beräkningsresurserna i noden poolen. Om konfigurerat kan det här scenariot skulle utlöser autoskalningen klustret om du vill distribuera ytterligare noder i poolen noden, men det kan ta några minuter för dessa noder att etablera och Tillåt Kubernetes-Schemaläggaren för att köra poddar på dem.

![Kubernetes burst skalning till ACI](media/concepts-scale/burst-scaling.png)

ACI kan du snabbt distribuera behållarinstanser utan ytterligare infrastruktur behöva. När du ansluter med AKS blir ACI en säker, logiska förlängning av ditt AKS-kluster. Virtual Kubelet-komponenten har installerats i AKS-klustret som presenterar ACI som en virtuell Kubernetes-nod. Kubernetes kan sedan schemalägga poddar som körs som ACI-instanser via virtuella noder och inte som poddar på VM-noder direkt i AKS-klustret.

Programmet kräver inga ändringar att använda virtuella noder. Distributioner kan skala över AKS och ACI och utan fördröjning som kluster autoskalningen distribuerar nya noderna i AKS-klustret.

Virtuella noder distribueras till en ytterligare undernät i samma virtuella nätverk som AKS-klustret. Den här virtuella nätverkskonfigurationen tillåter trafik mellan ACI och AKS skyddas. Precis som ett AKS-kluster är en säker, logiska beräkningsresurs som är isolerad från andra användare i en ACI-instans.

## <a name="next-steps"></a>Nästa steg

Kom igång med skalning av program, först följa den [Snabbstart för att skapa ett AKS-kluster med Azure CLI][aks-quickstart]. Du kan sedan starta manuellt eller automatiskt skala program i AKS-klustret:

- Skala manuellt [poddar] [ aks-manually-scale-pods] eller [noder][aks-manually-scale-nodes]
- Använd den [vågrät pod autoskalningen][aks-hpa]
- Använd den [kluster autoskalningen][aks-cluster-autoscaler]

Mer information om core Kubernetes och AKS förklaringar av begrepp finns i följande artiklar:

- [Kubernetes / AKS-kluster och arbetsbelastningar][aks-concepts-clusters-workloads]
- [Kubernetes / AKS åtkomst och identitet][aks-concepts-identity]
- [Kubernetes / AKS-säkerhet][aks-concepts-security]
- [Kubernetes / AKS virtuella nätverk][aks-concepts-network]
- [Kubernetes / AKS-lagring][aks-concepts-storage]

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

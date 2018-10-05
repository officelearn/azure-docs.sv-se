---
title: Azure Container Instances och orkestrering av behållare
description: Förstå hur Azure container instances interagera med behållarinitierare.
services: container-instances
author: seanmck
ms.service: container-instances
ms.topic: article
ms.date: 10/05/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: c17bdb5a81640a7162ae735a4633a31cdfffbb1d
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803519"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure Container Instances och behållarinitierare

Liten storlek och orientering för programmet är behållare bra för smidig leverans miljöer och mikrotjänstbaserade arkitekturer. Uppgiften att automatisera och hantera ett stort antal behållare och hur de samverkar kallas *orchestration*. Populära behållarinitierare är Kubernetes, DC/OS och Docker Swarm.

Azure Container Instances erbjuder några av de grundläggande funktionerna för schemaläggning av orchestration-plattformar. Och medan de inte täcker högre värde-tjänster som dessa plattformar tillhandahåller, Azure Container Instances kan komplettera dem. Den här artikeln beskriver omfånget för Azure Container Instances hanterar och hur fullständig behållarinitierare kan interagera med den.

## <a name="traditional-orchestration"></a>Traditionella orchestration

Standard definitionen av orchestration omfattar följande:

- **Schemaläggning**: baserat på en behållaravbildning och en resursbegäran, hitta en lämplig dator som du vill köra behållaren.
- **Tillhörighet/program-affinity**: ange att en uppsättning behållare ska köras i närheten varandra (för prestanda) eller tillräckligt långt ifrån varandra (för tillgänglighet).
- **Hälsoövervakning**: se för behållaren fel och automatiskt omboka dem.
- **Redundans**: hålla reda på vad som körs på varje dator och omboka behållare från datorer som inte fungerar till felfria noder.
- **Skala**: lägga till eller ta bort behållarinstanser för att möta efterfrågan, antingen manuellt eller automatiskt.
- **Nätverk**: Ange ett överlägg nätverk för att samordna behållare kan kommunicera över flera värddatorerna.
- **Tjänstidentifiering**: aktivera behållare för att hitta varandra automatiskt, även när de flyttas mellan värddatorerna och ändra IP-adresser.
- **Koordinerad programuppgraderingar**: hantera behållare uppgraderingar för att undvika driftavbrott och aktivera återställning om något går fel.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Orchestration med Azure Container Instances: en med lager

Azure Container Instances möjliggör en överlappande tillvägagångssättet för orkestrering, eftersom alla schemaläggning och hanteringsfunktioner som krävs för att köra en enskild behållare, samtidigt som orchestrator-plattformar för att hantera flera behållare uppgifter ovanpå den.

Eftersom den underliggande infrastrukturen för container instances hanteras av Azure, behöver inte en orchestrator-plattformen rör sig själv med att hitta en lämplig värd-dator som du vill köra en enskild behållare. Elasticiteten i molnet säkerställer att en alltid är tillgänglig. Orchestrator kan i stället fokusera på de aktiviteter som förenklar utvecklingen av flera behållare arkitekturer, inklusive skalning och koordinerade uppgraderingar.

## <a name="potential-scenarios"></a>Möjliga scenarier

Orchestrator-integrering med Azure Container Instances är fortfarande framväxande, vi förväntar oss att några olika miljöer kan dyker upp:

### <a name="orchestration-of-container-instances-exclusively"></a>Orkestrering av behållare instanser exklusivt

Eftersom de kom snabbt igång och faktureras per sekund, erbjuder en miljö som enbart utifrån Azure Container Instances det snabbaste sättet att komma igång och hantera med mycket varierande arbetsbelastningar.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Kombinationen av behållarinstanser och behållare i virtuella datorer

För tidskrävande och stabil arbetsbelastningar och är samordna behållare i ett kluster på dedikerade virtuella datorer vanligtvis billigare än samma behållare som körs med Azure Container Instances. Behållarinstanser erbjuder dock en bra lösning för att snabbt utöka och köpa exakt den totala kapaciteten att hantera oväntade eller tillfällig toppar i användningen.

I stället för att skala ut antalet virtuella datorer i klustret, och sedan distribuera ytterligare behållare till dessa datorer, orchestrator kan bara schemalägga ytterligare behållare i Azure Container Instances och ta bort dem när de är inte längre behövs.

## <a name="sample-implementation-virtual-kubelet-for-kubernetes"></a>Exempel på implementering: Virtual Kubelet för Kubernetes

Den [Virtual Kubelet] [ aci-connector-k8s] projekt visar hur orchestration behållarplattformar kan integreras med Azure Container Instances.

Virtual Kubelet imiterar Kubernetes [kubelet] [ kubelet-doc] genom att registrera som en nod med obegränsad kapacitet och skicka skapandet av [poddar] [ pod-doc] som behållargrupper i Azure Container Instances.

Anslutningsappar för andra initierare kan byggas som integreras på samma sätt med plattformen primitiver att kombinera kraften hos orchestrator API snabbt och enkelt med hantering av behållare i Azure Container Instances.

## <a name="next-steps"></a>Nästa steg

Skapa din första behållare med Azure Container Instances med hjälp av den [Snabbstartsguide](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/virtual-kubelet/tree/master/providers/azure
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
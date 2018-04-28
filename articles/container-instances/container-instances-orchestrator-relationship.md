---
title: Azure Behållarinstanser och behållare orchestration
description: Förstå hur Azure-behållaren instanser interagera med behållaren orchestrators.
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/23/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: e1455cba004facfa03dca21544eec754f5dc60be
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure Container instanser och behållare orchestrators

Behållare är väl lämpade för flexibel leverans miljöer och mikrotjänster-baserade arkitekturer på grund av deras liten storlek och programmet orientering. Uppgiften att automatisera och hantera ett stort antal behållare och hur de samverkar kallas *orchestration*. Populära behållaren orchestrators inkluderar Kubernetes DC/OS och Docker Swarm.

Behållarinstanser som Azure tillhandahåller en del av grundläggande funktionerna för schemaläggning av orchestration-plattformar. Och när de inte täcker högre värde tjänster som dessa plattformar tillhandahåller, Azure Behållarinstanser kan vara kompletterande till dem. Den här artikeln beskriver vilka Azure Behållarinstanser hanterar och hur fullständig behållaren orchestrators kan interagera med den.

## <a name="traditional-orchestration"></a>Traditionella orchestration

Standard definitionen av orchestration omfattar följande aktiviteter:

- **Schemalägga**: baserat på en avbildning av behållare och en resursbegäran om, hitta en lämplig dator som ska köras i behållaren.
- **Tillhörighet/mot-affinity**: ange att en uppsättning behållare ska köras i närheten varandra (för prestanda) eller tillräckligt långt ifrån varandra (för tillgänglighet).
- **Hälsoövervakning**: Titta på för behållaren och automatiskt schemalägga dem.
- **Redundans**: hålla reda på vad som körs på varje dator och schemalägga behållare från datorer som inte fungerar felfritt noder.
- **Skalning**: lägga till eller ta bort behållarinstanser för att möta efterfrågan, antingen manuellt eller automatiskt.
- **Nätverk**: Ange ett överlägget nätverk för att samordna behållare kan kommunicera över flera datorer på värden.
- **Identifiering av tjänst**: aktivera behållare att hitta varandra automatiskt, även när de flyttar mellan värddatorerna och ändra IP-adresser.
- **Koordineras programuppgraderingar**: hantera behållare uppgraderingar för att undvika driftavbrott och aktivera återställning om något går fel.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Orchestration med Azure Container instanser: överlappande tillvägagångssättet

Azure Container-instanser kan en överlappande tillvägagångssättet för orchestration, eftersom alla schemaläggning och hanteringsfunktioner som krävs för att köra en enskild behållare, samtidigt som orchestrator-plattformar för att hantera flera behållare uppgifter ovanpå den.

Eftersom den underliggande infrastrukturen för behållarinstanser som hanteras av Azure, behöver en orchestrator-plattformen inte rör sig själv med att hitta en lämplig värd-dator som kör en enskild behållare. Elasticitet för molnet garanterar att en alltid är tillgänglig. Orchestrator kan istället fokusera på uppgifter som förenklar utvecklingen av flera behållare arkitekturer, inklusive skalning och samordnade uppgraderingar.

## <a name="potential-scenarios"></a>Möjliga scenarier

Orchestrator-integrering med Azure Container instanser är fortfarande framväxande, förutse att några olika miljöer kan du se ett mönster:

### <a name="orchestration-of-container-instances-exclusively"></a>Dirigering av behållare enbart instanser

Eftersom de komma igång snabbt och debiterar med andra erbjuder en miljö som baseras enbart på Azure-Behållarinstanser det snabbaste sättet att komma igång och att hantera mycket variabel arbetsbelastningar.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Kombinationen av behållarinstanser och behållare i virtuella datorer

Långvariga stabil arbetsbelastningar är samordna behållare i ett kluster på dedikerade virtuella datorer vanligtvis billigare än samma behållare som körs med Azure Container instanser. Behållarinstanser erbjuder dock en bra lösning för snabbt expanderande och tilldelning av kontrakt din totala kapaciteten att hantera oväntade eller tillfällig toppar i användning.

I stället för att skala ut antalet virtuella datorer i klustret och sedan distribuera ytterligare behållare på de datorerna orchestrator kan bara schemalägga ytterligare behållare i Azure Behållarinstanser och ta bort dem när de inte längre behövs.

## <a name="sample-implementation-azure-container-instances-connector-for-kubernetes"></a>Exempel på implementering: Azure Container instanser Connector för Kubernetes

Vi har börjat skapa för att demonstrera hur behållaren orchestration plattformar kan integrera med Azure Container instanser, en [exempel connector för Kubernetes][aci-connector-k8s].

Anslutningen till Kubernetes efterliknar den [kubelet] [ kubelet-doc] genom att registrera som en nod med obegränsad kapacitet och sändning av skapandet av [skida] [ pod-doc] som behållargrupper i Azure Container instanser.

Kopplingar för andra orchestrators kan byggas som på samma sätt integreras med plattformen primitiver kombinera kraften i orchestrator API med hastighet och enkelhet för att hantera behållare i Azure Container instanser.

> [!WARNING]
> ACI kopplingen för Kubernetes *experiment* och ska inte användas i produktionen.

## <a name="next-steps"></a>Nästa steg

Skapa din första behållare med Azure Container instanser med hjälp av den [Snabbstartsguide](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/virtual-kubelet/tree/master/providers/azure
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
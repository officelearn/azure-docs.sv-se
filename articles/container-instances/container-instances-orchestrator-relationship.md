---
title: "Azure-Behållarinstanser och behållare Orchestration"
description: "Förstå hur Azure Behållarinstanser interagera med behållaren orchestrators"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 07/24/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: a51e746c501cca0521972b09d145439348d1d22d
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure Container instanser och behållare orchestrators

Behållare är väl lämpade för flexibel leverans miljöer och mikrotjänster-baserade arkitekturer på grund av deras liten storlek och programmet orientering. Uppgiften att automatisera och hantera ett stort antal behållare och hur de samverkar kallas *orchestration*. Populära behållaren orchestrators inkluderar Kubernetes DC/OS och Docker Swarm som är tillgängliga i den [Azure Container Service](https://docs.microsoft.com/azure/container-service/).

Azure Behållarinstanser ger några av de grundläggande funktionerna för schemaläggning av orchestration-plattformar, men de täcker inte högre värde tjänster som dessa plattformar ger och kan i praktiken vara kompletterande med dem. Den här artikeln beskriver vilka Azure Behållarinstanser hanterar och hur fullständig behållaren orchestrators kan interagera med den.

## <a name="traditional-orchestration"></a>Traditionella orchestration

Standard definitionen av orchestration omfattar följande aktiviteter:

- **Schemalägga**: baserat på en avbildning av behållare och en resursbegäran om, hitta en lämplig dator som ska köras i behållaren.
- **Tillhörighet/mot-affinity**: ange att en uppsättning behållare ska köras i närheten varandra (för prestanda) eller tillräckligt långt ifrån varandra (för tillgänglighet).
- **Hälsoövervakning**: Titta på för behållaren och automatiskt schemalägga dem.
- **Redundans**: hålla reda på vad som körs på varje dator och schemalägga behållare från datorer som inte fungerar felfritt noder.
- **Skalning**: lägga till eller ta bort behållarinstanser för att möta efterfrågan, antingen manuellt eller automatiskt.
- **Nätverk**: Ange ett överlägget nätverk för att samordna behållare kan kommunicera över flera datorer på värden.
- **Identifiering av tjänst**: aktivera behållare att hitta varandra automatiskt även när de flyttar mellan värddatorerna och ändra IP-adresser.
- **Koordineras programuppgraderingar**: hantera behållare uppgraderingar för att undvika programmet stillestånd och aktivera återställning om något går fel.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Orchestration med Azure Container instanser: överlappande tillvägagångssättet

Azure Container-instanser kan en överlappande tillvägagångssättet för orchestration, eftersom alla schemaläggning och hanteringsfunktioner som krävs för att köra en enskild behållare, samtidigt som orchestrator-plattformar för att hantera flera behållare uppgifter ovanpå den.

Eftersom alla underliggande infrastruktur för Behållarinstanser som hanteras av Azure, behöver inte en orchestrator-plattformen rör sig själv med att hitta en lämplig värd-dator som kör en enskild behållare. Elasticitet för molnet garanterar att en alltid är tillgänglig. Orchestrator kan istället fokusera på uppgifter som förenklar utvecklingen av flera behållare arkitekturer, inklusive skalning och samordnade uppgraderingar.



## <a name="potential-scenarios"></a>Möjliga scenarier

Orchestrator-integrering med Azure Container instanser är fortfarande framväxande, förutse att några olika miljöer kan du se ett mönster:

### <a name="orchestration-of-container-instances-exclusively"></a>Dirigering av Behållarinstanser exklusivt

Eftersom de komma igång snabbt och debiterar med andra erbjuder en miljö som baseras enbart på Azure-Behållarinstanser det snabbaste sättet att komma igång och att hantera mycket variabel arbetsbelastningar.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Kombinationen av Behållarinstanser och behållare i virtuella datorer

För tidskrävande, stabil arbetsbelastningar blir samordna behållare i ett kluster på dedikerade virtuella datorer vanligtvis billigare än samma behållare som körs med Behållarinstanser. Behållarinstanser erbjuder dock en bra lösning för snabbt expanderande och tilldelning av kontrakt din totala kapaciteten att hantera oväntade eller tillfällig toppar i användning. I stället för att skala ut antalet virtuella datorer i klustret och sedan distribuera ytterligare behållare på de datorerna orchestrator kan bara schemalägga ytterligare behållare med Behållarinstanser och ta bort dem när de inte längre behövs.

## <a name="sample-implementation-azure-container-instances-connector-for-kubernetes"></a>Exempel på implementering: Azure Container instanser Connector för Kubernetes

Vi har börjat skapa för att demonstrera hur behållaren orchestration plattformar kan integrera med Azure Container instanser, en [exempel connector för Kubernetes][aci-connector-k8s].

Anslutningen till Kubernetes efterliknar den [kubelet] [ kubelet-doc] genom att registrera som en nod med obegränsad kapacitet och sändning av skapandet av [skida] [ pod-doc] som behållargrupper i Azure Container instanser.

<!-- ![ACI Connector for Kubernetes][aci-connector-k8s-gif] -->

Kopplingar för andra orchestrators kan byggas som på samma sätt är integrerad med plattformen primitiver kombinera kraften i orchestrator API med hastighet och enkelhet för att hantera behållare i Azure Container instanser.

> [!WARNING]
> ACI kopplingen för Kubernetes *experiment* och ska inte användas i produktionen.

## <a name="next-steps"></a>Nästa steg

Skapa din första behållare med Azure Container instanser med hjälp av den [Snabbstartsguide](container-instances-quickstart.md).

<!-- IMAGES -->
[aci-connector-k8s-gif]: ./media/container-instances-orchestrator-relationship/aci-connector-k8s.gif

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/azure/aci-connector-k8s
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
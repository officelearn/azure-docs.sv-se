---
title: Behållarinstanser och behållarorkestrering
description: Förstå hur Azure-behållarinstanser interagerar med behållare orchestrators.
ms.topic: article
ms.date: 04/15/2019
ms.custom: mvc
ms.openlocfilehash: f3f8693d1a9a12e7c35d126ab3e3ca53448e5e40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533666"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure Container-instanser och behållarorketorer

På grund av sin ringa storlek och programorientering är behållare väl lämpade för agila leveransmiljöer och mikrotjänstbaserade arkitekturer. Uppgiften att automatisera och hantera ett stort antal behållare och hur de interagerar kallas *orkestrering*. Populära container orchestrators inkluderar Kubernetes, DC / OS och Docker Swarm.

Azure Container Instances innehåller några av de grundläggande schemaläggningsfunktionerna för orchestration-plattformar. Och även om det inte täcker de tjänster med högre värde som dessa plattformar tillhandahåller, kan Azure Container Instances komplettera dem. I den här artikeln beskrivs omfattningen av vad Azure Container Instances hanterar och hur fullständiga behållarorkestratorer kan interagera med den.

## <a name="traditional-orchestration"></a>Traditionell orkestrering

Standarddefinitionen för orkestrering innehåller följande uppgifter:

- **Schemaläggning**: Med tanke på en behållaravbildning och en resursbegäran kan du hitta en lämplig dator där behållaren ska köras.
- **Tillhörighet/Anti-affinitet**: Ange att en uppsättning behållare ska köras i närheten av varandra (för prestanda) eller tillräckligt långt ifrån varandra (för tillgänglighet).
- **Hälsoövervakning**: Håll utkik efter behållarfel och schemalägg dem automatiskt.
- **Redundans:** Håll reda på vad som körs på varje dator och schemalägg om behållare från misslyckade datorer till felfria noder.
- **Skalning**: Lägg till eller ta bort behållarinstanser för att matcha efterfrågan, antingen manuellt eller automatiskt.
- **Nätverk:** Tillhandahålla ett överläggsnätverk för samordning av behållare för att kommunicera mellan flera värddatorer.
- **Identifiering av**tjänsten : Aktivera behållare för att hitta varandra automatiskt, även när de flyttas mellan värddatorer och ändrar IP-adresser.
- **Samordnade programuppgraderingar**: Hantera containeruppgraderingar för att undvika stillestånd från programmet och aktivera återställning om något går fel.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Orkestrering med Azure Container-instanser: En metod i flera lager

Azure Container Instances möjliggör en metod i flera lager till orkestrering, som tillhandahåller alla schemaläggnings- och hanteringsfunktioner som krävs för att köra en enda behållare, samtidigt som orchestrator-plattformar kan hantera uppgifter med flera behållare ovanpå den.

Eftersom den underliggande infrastrukturen för behållarinstanser hanteras av Azure behöver en orchestrator-plattform inte ägna sig åt att hitta en lämplig värddator som du kan köra en enda behållare på. Molnets elasticitet säkerställer att en alltid är tillgänglig. I stället kan orchestrator fokusera på de uppgifter som förenklar utvecklingen av arkitekturer med flera behållare, inklusive skalning och samordnade uppgraderingar.

## <a name="scenarios"></a>Scenarier

Orchestrator-integrering med Azure Container Instances är fortfarande begynnande, men vi räknar med att några olika miljöer kommer att uppstå:

### <a name="orchestration-of-container-instances-exclusively"></a>Orkestrering av behållarinstanser uteslutande

Eftersom de börjar snabbt och fakturerar för den andra, erbjuder en miljö som uteslutande baseras på Azure Container Instances det snabbaste sättet att komma igång och hantera mycket varierande arbetsbelastningar.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Kombination av behållarinstanser och behållare i virtuella datorer

För långvariga, stabila arbetsbelastningar är orkestrering av behållare i ett kluster av dedikerade virtuella datorer vanligtvis billigare än att köra samma behållare med Azure Container Instances. Behållarinstanser erbjuder dock en bra lösning för att snabbt utöka och kontraktera din totala kapacitet för att hantera oväntade eller kortlivade toppar i användningen.

I stället för att skala ut antalet virtuella datorer i klustret och sedan distribuera ytterligare behållare på dessa datorer, kan orchestrator helt enkelt schemalägga ytterligare behållare i Azure Container Instances och ta bort dem när de inte längre är längre Behövs.

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>Exempelimplementering: virtuella noder för Azure Kubernetes Service (AKS)

Om du snabbt vill skala programarbetsbelastningar i ett AKS-kluster [(Azure Kubernetes Service)](../aks/intro-kubernetes.md) kan du använda *virtuella noder som* skapats dynamiskt i Azure Container Instances. Virtuella noder möjliggör nätverkskommunikation mellan poddar som körs i ACI och AKS-klustret. 

Virtuella noder stöder för närvarande Linux-behållarinstanser. Kom igång med virtuella noder med [Azure CLI-](https://go.microsoft.com/fwlink/?linkid=2047538) eller [Azure-portalen](https://go.microsoft.com/fwlink/?linkid=2047545).

Virtuella noder använder [virtual kubelet][aci-connector-k8s] med öppen källkod för att efterlikna Kubernetes [kubelet][kubelet-doc] genom att registrera sig som en nod med obegränsad kapacitet. Den virtuella Kubelet skickar skapandet av [poddar][pod-doc] som behållargrupper i Azure Container Instances.

Se [Projektet Virtual Kubelet](https://github.com/virtual-kubelet/virtual-kubelet) för ytterligare exempel på hur du utökar Kubernetes API till serverlösa behållarplattformar.

## <a name="next-steps"></a>Nästa steg

Skapa din första behållare med Azure Container Instances med hjälp av [snabbstartsguiden](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/azure-aci
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

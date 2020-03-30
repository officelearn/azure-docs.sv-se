---
title: Översikt över Azure Monitor för behållare | Microsoft-dokument
description: I den här artikeln beskrivs Azure Monitor för behållare som övervakar AKS Container Insights-lösning och det värde som levereras genom att övervaka hälsotillståndet för dina AKS-kluster och behållarinstansar i Azure.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 3ff2c35ae9f5838447ce90e2a020649427920a43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275235"
---
# <a name="azure-monitor-for-containers-overview"></a>Översikt över Azure Monitor för containrar

Azure Monitor for containers är en funktion som utformats för att övervaka prestanda för behållararbetsbelastningar som distribueras till:

- Hanterade Kubernetes-kluster som finns på [Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md)
- Självhanterade Kubernetes-kluster som finns på Azure med [AKS Engine](https://github.com/Azure/aks-engine)
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- Självhanterade Kubernetes-kluster som finns på [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) eller lokalt
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

Azure Monitor för behållare stöder kluster som kör operativsystemet Linux och Windows Server 2019. 

Övervakning av dina behållare är viktigt, särskilt när du kör ett produktionskluster, i stor skala, med flera program.

Azure Monitor för behållare ger dig prestandasynlighet genom att samla in minnes- och processormått från styrenheter, noder och behållare som är tillgängliga i Kubernetes via Metrics API. Containerloggar samlas också.  När du har aktiverat övervakning från Kubernetes kluster samlas mått och loggar automatiskt in åt dig via en behållarversion av Log Analytics-agenten för Linux. Mått skrivs till måtten lagra och loggdata skrivs till loggar arkivet som är associerad med din [Log Analytics](../log-query/log-query-overview.md) arbetsyta. 

![Azure Monitor för behållare arkitektur](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Vad tillhandahåller Azure Monitor för behållare?

Azure Monitor för behållare ger en omfattande övervakningsupplevelse med hjälp av olika funktioner i Azure Monitor. Med de här funktionerna kan du förstå prestanda och hälsa för kubernetes-klustret som kör operativsystemet Linux och Windows Server 2019 och behållararbetsbelastningarna. Med Azure Monitor för behållare kan du:

* Identifiera AKS-behållare som körs på noden och deras genomsnittliga processor- och minnesanvändning. Den här kunskapen kan hjälpa dig att identifiera flaskhalsar i resursen.
* Identifiera processor- och minnesanvändning av behållargrupper och deras behållare som finns i Azure Container Instances.  
* Identifiera var behållaren finns i en styrenhet eller en pod. Den här kunskapen kan hjälpa dig att visa handkontrollens eller poddens övergripande prestanda. 
* Granska resursutnyttjandet av arbetsbelastningar som körs på värden som inte är relaterade till de standardprocesser som stöder podden.
* Förstå beteendet hos klustret under genomsnittliga och tyngsta belastningar. Den här kunskapen kan hjälpa dig att identifiera kapacitetsbehov och bestämma den maximala belastning som klustret kan upprätthålla. 
* Konfigurera aviseringar så att du proaktivt meddelar dig eller registrerar det när CPU- och minnesanvändning på noder eller behållare överskrider dina tröskelvärden, eller när en hälsotillståndsändring sker i klustret vid infrastrukturen eller noder hälsosammanslagning.
* Integrera med [Prometheus](https://prometheus.io/docs/introduction/overview/) för att visa program- och arbetsbelastningsmått som samlas in från noder och Kubernetes med hjälp av [frågor](container-insights-log-search.md) för att skapa anpassade aviseringar, instrumentpaneler och detaljerad analys.
* Övervaka behållararbetsbelastningar [som distribueras till AKS](https://github.com/Azure/aks-engine) Engine lokalt och [AKS-motor på Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
* Övervaka behållararbetsbelastningar [som distribueras till Azure Red Hat OpenShift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >Stöd för Azure Red Hat OpenShift är en funktion i offentlig förhandsversion just nu.
    >

De största skillnaderna i övervakningen av ett Windows Server-kluster jämfört med ett Linux-kluster är följande:

- Minnes-RSS-mått är inte tillgängligt för Windows-nod och behållare.
- Information om disklagringskapacitet är inte tillgänglig för Windows-noder.
- Behållarloggar är inte tillgängliga för behållare som körs i Windows-noder.
- Stöd för livedata (förhandsversion) är tillgängligt med undantag för Windows-behållarloggar.
- Endast pod-miljöer övervakas, inte Docker-miljöer.
- I och med förhandsversionen stöds högst 30 Windows Server-behållare. Den här begränsningen gäller inte linuxbehållare. 

Kolla in följande video som ger en djupdykning på mellannivå som hjälper dig att lära dig mer om att övervaka DITT AKS-kluster med Azure Monitor för behållare.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Hur kommer jag åt den här funktionen?

Du kan komma åt Azure Monitor för behållare på två sätt, från Azure Monitor eller direkt från det valda AKS-klustret. Från Azure Monitor har du ett globalt perspektiv på alla distribuerade behållare, som övervakas och som inte är det, så att du kan söka och filtrera över dina prenumerationer och resursgrupper och sedan detaljgranska Azure Monitor för behållare från vald behållare.  Annars kan du komma åt funktionen direkt från en vald AKS-behållare från AKS-sidan.  

![Översikt över metoder för åtkomst till Azure Monitor för behållare](./media/container-insights-overview/azmon-containers-experience.png)

Om du är intresserad av att övervaka och hantera docker- och Windows-behållarvärdar som körs utanför AKS för att visa konfiguration, granskning och resursanvändning läser du [lösningen för behållarövervakning](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Nästa steg

Om du vill börja övervaka kubernetes-klustret läser du [Så här aktiverar du Azure Monitor för behållare för](container-insights-onboard.md) att förstå kraven och tillgängliga metoder för att aktivera övervakning. 

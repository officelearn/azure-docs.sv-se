---
title: Översikt över Azure Monitor för behållare | Microsoft Docs
description: I den här artikeln beskrivs Azure Monitor för behållare som övervakar AKS container Insights-lösning och det värde den ger genom att övervaka hälso tillståndet för dina AKS-kluster och Container Instances i Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 08/02/2019
ms.openlocfilehash: 8a6a9862679508971edd23b2d4220446c2692d72
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555374"
---
# <a name="azure-monitor-for-containers-overview"></a>Översikt över Azure Monitor för behållare

Azure Monitor för behållare är en funktion som har utformats för att övervaka prestanda för behållar arbets belastningar som distribueras till antingen Azure Container Instances eller hanterade Kubernetes-kluster som finns i Azure Kubernetes service (AKS). Övervakning av behållare är avgörande, särskilt när du kör ett produktions kluster, i skala med flera program.

Azure Monitor för behållare ger dig prestanda synlighet genom att samla in minnes-och processor mått från styrenheter, noder och behållare som är tillgängliga i Kubernetes via Metrics-API: et. Containerloggar samlas också.  När du har aktiverat övervakning från Kubernetes-kluster samlas mått och loggar in automatiskt åt dig via en behållar version av Log Analytics agent för Linux. Måtten skrivs till mått arkivet och loggdata skrivs till logg lagret som är kopplat till din [Log Analytics](../log-query/log-query-overview.md) -arbetsyta. 

![Azure Monitor för behållare arkitektur](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Vad ger Azure Monitor för behållare?

Azure Monitor för behållare ger en omfattande övervaknings upplevelse med olika funktioner i Azure Monitor så att du kan förstå prestanda och hälsa för ditt Kubernetes-kluster och behållar arbets belastningarna. Med Azure Monitor för behållare kan du:

* Identifiera AKS-behållare som körs på noden och deras genomsnittliga processor-och minnes användning. Den här kunskapen kan hjälpa dig att identifiera resurs Flask halsar.
* Identifiera processor-och minnes användning för behållar grupper och deras behållare som finns i Azure Container Instances.  
* Identifiera var behållaren finns i en styrenhet eller en pod. Den här kunskapen kan hjälpa dig att visa enhetens eller Pod allmänna prestanda.
* Granska resursutnyttjande för arbets belastningar som körs på värden och som inte är relaterade till de standard processer som stöder pod.
* Förstå klustrets beteende i genomsnitt och tyngsta belastningar. Den här kunskapen kan hjälpa dig att identifiera kapacitets behov och fastställa den maximala belastning som klustret kan hantera. 
* Konfigurera aviseringar för att proaktivt meddela dig eller registrera den när processor-och minnes användning på noder eller behållare överskrider tröskelvärdena.
* Integrera med [Prometheus](https://prometheus.io/docs/introduction/overview/) för att visa program-och arbets belastnings mått som samlas in från noder och Kubernetes med hjälp av [frågor](container-insights-log-search.md) för att skapa anpassade aviseringar, instrument paneler och detaljerad analys.

    >[!NOTE]
    >Stöd för Prometheus är en funktion i offentlig för hands version för tillfället.
    >

* Övervaka arbets belastningar [för behållare distribuerade till AKS-motor](https://github.com/microsoft/OMS-docker/tree/aks-engine).

Titta på följande videoklipp med en mellanliggande nivå för att hjälpa dig att lära dig mer om att övervaka ditt AKS-kluster med Azure Monitor för behållare.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Hur gör jag för att åtkomst till den här funktionen?

Du kan komma åt Azure Monitor för behållare på två sätt, från Azure Monitor eller direkt från det valda AKS-klustret. Från Azure Monitor har du ett globalt perspektiv för alla behållare som distribueras, som övervakas och som inte är så att du kan söka efter och filtrera i dina prenumerationer och resurs grupper och sedan gå vidare till Azure Monitor för behållare från valda behållaren.  Annars kan du komma åt funktionen direkt från en vald AKS-behållare från sidan AKS.  

![Översikt över metoder för att komma åt Azure Monitor för behållare](./media/container-insights-overview/azmon-containers-experience.png)

Om du är intresse rad av att övervaka och hantera dina Docker-och Windows-behållarobjekt som körs utanför AKS för att Visa konfigurations-, gransknings-och resursutnyttjande, se [lösningen för övervakning av behållare](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Nästa steg

Om du vill börja övervaka ditt AKS-kluster läser du [så här aktiverar du Azure Monitor för behållare](container-insights-onboard.md) för att förstå kraven och tillgängliga metoder för att aktivera övervakning.  

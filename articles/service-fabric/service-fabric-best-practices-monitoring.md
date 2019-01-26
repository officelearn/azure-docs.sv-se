---
title: Azure Service Fabric monitoring Metodtips | Microsoft Docs
description: Metodtips för att övervaka Service Fabric-kluster och program.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: jeanpaul.connock
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 152a503181bba9e1ed39671794d512258759e251
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54914182"
---
# <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik

[Övervakning och diagnostik](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) är viktiga för utveckling, testning och distribution av arbetsbelastningar i alla miljöer i molnet. Exempelvis kan du spåra hur dina program används kan de åtgärder som vidtas av Service Fabric-plattformen, resursanvändningen med prestandaräknare och den övergripande hälsan för klustret. Du kan använda den här informationen för att diagnostisera och åtgärda problem och förebygga att de uppstår i framtiden.

## <a name="application-monitoring"></a>Programövervakning

Programövervakning spårar hur funktioner och komponenter i ditt program används. Övervaka dina program för att se till att problem som påverkar användarna fångas. Programövervakning ansvarar för de utvecklar programmet och dess tjänster eftersom den är unik för affärslogiken i programmet. Vi rekommenderar att du ställer in programövervakning med [Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet), Azures verktyg för programövervakning.

## <a name="cluster-monitoring"></a>Klusterövervakning

Ett av målen för Service Fabric är att göra program motståndskraftig mot maskinvarufel. Det här målet uppnås via plattformens system services möjligheten att identifiera problem med infrastruktur och snabbt redundans arbetsbelastningar till andra noder i klustret. Men vad händer om systemtjänster själva har problem med? Eller om du i försök att distribuera eller flytta en arbetsbelastning kan reglerna för placering av tjänster har brutits? Service Fabric tillhandahåller diagnostik för dessa och andra problem med att kontrollera att du informeras om hur Service Fabric-plattformen samverkar med program, tjänster, behållare och noder.

För Windows-kluster, rekommenderar vi att du ställer in klusterövervakning med [Diagnostikagenten](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) och [Log Analytics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup).

Log Analytics är också det rekommenderade verktyget för Azure-plattformen och övervakning av infrastruktur för Linux-kluster. Linux-plattformen diagnostik kräver olika konfigurationer som anges i [händelser för Service Fabric Linux-kluster i Syslog](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog).

## <a name="infrastructure-monitoring"></a>Infrastrukturövervakning

[Log Analytics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent) rekommenderas för övervakning nivå klusterhändelser. När du har konfigurerat Log Analytics-agenten med arbetsytan enligt beskrivningen i föregående länk kommer du att kunna samla in prestandavärden, till exempel processoranvändning, .NET prestandaräknare, till exempel processen på processoranvändning, Service Fabric-prestanda räknare, till exempel antal undantag från en tillförlitlig tjänst och behållarmätvärden, till exempel CPU-användning.  Du måste skriva behållarloggarna stdout och stderr så att de blir tillgängliga i Log Analytics.

## <a name="watchdogs"></a>Watchdogs

I allmänhet är en watchdog en separat tjänst som bevakar hälso- och belastningen över tjänster, pingar slutpunkter och rapporterar oväntat hälsohändelser i klustret. Detta kan förhindra fel som inte kanske identifieras endast baserat på prestanda för en enskild tjänst. Watchdogs är också ett bra ställe att värdhantera kod som utför åtgärder som inte kräver användaråtgärder som att rensa loggfiler i storage vid visst tidsintervall. Se ett exempel watchdog implementering i [händelser för Service Fabric Linux-kluster i Syslog](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="next-steps"></a>Nästa steg

* Kom igång instrumentering av ditt program: [Genereringen av program på händelse- och log](service-fabric-diagnostics-event-generation-app.md).
* Gå igenom stegen för att ställa in Application Insights för ditt program med [övervaka och diagnostisera ett ASP.NET Core-program i Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Lär dig mer om hur du övervakar plattformen och de händelser som Service Fabric tillhandahåller åt dig: [Genereringen av plattform på händelse- och log](service-fabric-diagnostics-event-generation-infra.md).
* Konfigurera Log Analytics-integrering med Service Fabric: [Konfigurera Log Analytics för ett kluster](service-fabric-diagnostics-oms-setup.md)
* Lär dig hur du ställer in Log Analytics för att övervaka behållare: [Övervakning och diagnostik för Windows-behållare i Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Se exempel diagnostik problem och lösningar med Service Fabric: [diagnostisera vanliga scenarier](service-fabric-diagnostics-common-scenarios.md)
* Läs mer om allmänna övervakning rekommendationer för Azure-resurser: [Metodtips – övervakning och diagnostik](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
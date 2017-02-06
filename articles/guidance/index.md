---
title: "Riktlinjer för Azure | Microsoft Docs"
description: "Metodtips och riktlinjer för Azure"
services: 
documentationcenter: na
author: bennage
manager: marksou
editor: 
tags: 
ms.assetid: de94c74a-fea7-4815-8484-553e421a7490
ms.service: guidance
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: christb
translationtype: Human Translation
ms.sourcegitcommit: 5f3ced657cf3d6587a63789b3dd3ca41cd2856f0
ms.openlocfilehash: 0061e1ff2ae2d6b8ed7b7c3bb60405e76d4cc91b


---
# <a name="azure-guidance"></a>Azure-vägledning
[!INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Microsofts team för mönster och rutiner är en del av Azures kundrådgivningsteam. Vårt syfte är att hjälpa utvecklare, arkitekter och IT-proffs att hantera Microsoft Azure-plattformen. Vi tar fram vägledningsinformation med metodtips för att skapa molnlösningar i Azure.

## <a name="checklists"></a>Checklistor
De här listorna är en översikt över granskning av grundläggande aspekter av tillgänglighet och skalbarhet. 

* [Checklista för tillgänglighet][AvailabilityChecklist] 
  
    En sammanfattning av rekommenderade metoder för att säkerställa tillgänglighet.
* [Checklista för skalbarhet][ScalabilityChecklist]
  
    En sammanfattning av rekommenderade metoder för att utforma och implementera skalbara tjänster och datahantering.

## <a name="best-practices-articles"></a>Artiklar med metodtips
Dessa artiklar innehåller en detaljerad beskrivning av viktiga begrepp som ofta är kopplade till molntjänster. 

* [API-design][APIDesign] 
  
    En beskrivning av faktorer att tänka på när du utformar en webb-API.
* [API-implementering][APIImplementation] 
  
    En uppsättning rekommenderade metoder för att implementera och publicera en webb-API.
* [Vägledning om API-säkerhet](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 
  
    En beskrivning av problem med autentisering och auktorisering (till exempel tokentyper, auktoriseringsprotokoll, auktoriseringsflöden och hotminskning).
* [Riktlinjer för automatisk skalning][AutoscalingGuidance] 
  
    En sammanfattning av överväganden för att skala lösningar utan att behöva ingripa manuellt.
* [Riktlinjer för bakgrundsjobb][BackgroundJobsGuidance] 
  
    En beskrivning av tillgängliga alternativ och rekommenderade metoder för att implementera uppgifter som ska utföras i bakgrunden, oberoende av alla åtgärder i förgrunden eller interaktiva funktioner.
* [Riktlinjer för CDN (Content Delivery Network)][CDNGuidance] 
  
    Allmänna riktlinjer och rekommenderade metoder för att använda CDN och minska belastningen på dina program samt maximera tillgänglighet och prestanda.
* [Riktlinjer för cachelagring][CachingGuidance] 
  
    En sammanfattning av hur du använder cachelagring för att förbättra prestanda och skalbarhet i ett system.
* [Riktlinjer för datapartitionering][DataPartitioningGuidance]
  
    Strategier du kan använda för att partitionera data för att förbättra skalbarheten, minska konkurrensen och optimera prestanda.
* [Riktlinjer för övervakning och diagnostik][MonitoringandDiagnosticsGuidance] 
  
    Vägledning för att spåra hur användarna använder systemet, spåra resursutnyttjande och i allmänhet övervaka systemets hälsotillstånd och prestanda.
* [Rekommenderade namngivningskonventioner][naming-conventions] 
  
    Rekommenderade namnkonventioner för Azure-resurser.
* [Allmänna riktlinjer för återförsök][RetryGeneralGuidance] 
  
    Beskrivning av de allmänna principerna för hantering av tillfälligt fel.
* [Tjänstspecifika riktlinjer för återförsök][RetryServiceSpecificGuidance]
  
    En sammanfattning av återförsöksfunktioner för många Azure-tjänster, däribland information om hur du ska använda, anpassa och utöka återförsöksmekanismen för tjänsten.

## <a name="scenario-guides"></a>Scenarioguider
* [Köra Elasticsearch i Azure][elasticsearch] 
  
    Elasticsearch är en mycket skalbar sökmotor och databas med öppen källkod. Den är lämplig för situationer som kräver snabb analys och identifiering av information som finns i stora datauppsättningar. Den här vägledningen innehåller en rad viktiga aspekter att tänka på när du utformar ett Elasticsearch-kluster.
* [Identitetshantering för program med flera klienter][identity-multitenant] 
  
    Flera organisationer i samma installation (multitenancy) är en arkitektur där flera klienter delar ett program men isoleras från varandra. Den här vägledningen beskriver hur du hanterar användaridentiteter i ett program för flera klienter och hur du använder [Azure Active Directory][AzureAD] för att hantera inloggning och autentisering.
* [Utveckla stordatalösningar](https://msdn.microsoft.com/library/dn749874.aspx)
  
    I den här handboken beskrivs användningen av HDInsight för scenarier som iterativ undersökning, som ett informationslager, för ETL-processer och integrering i befintliga BI-system. Den innehåller också anvisningar för att förstå begreppet stordata, planera och utforma stordatalösningar och implementera lösningarna.

## <a name="patterns"></a>Mönster
* [Designmönster för molnet: anvisningar om förebyggande arkitektur för molnprogram](https://msdn.microsoft.com/library/dn568099.aspx)
  
    Designmönster för molnet är ett bibliotek med designmönster och relaterade vägledningsämnen. Det tydliggör fördelarna med att applicera mönster genom att visa hur varje del får plats i molnarkitekturprogrammet.
* [Optimera prestanda för molnprogram](https://github.com/mspnp/performance-optimization)
  
    I den här vägledningen beskrivs vanliga antimönster som hindrar appar från skalning under belastning. Den innehåller exempel som visar åtta antimönster och en [prestandaanalysbeskrivning](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md) samt en guide för [utvärdering av prestanda mot nyckelvärden](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md).

## <a name="reference-architectures"></a>Referensarkitekturer
Våra referensarkitekturer ordnas efter scenario.
Varje enskild arkitektur innehåller rekommendationer, ingående anvisningar och en körbar komponent som återspeglar rekommendationerna.

Det aktuella biblioteket med referensarkitektur är tillgängligt på [http://aka.ms/architecture](http://aka.ms/architecture).

## <a name="resiliency-guidance"></a>Återhämtningsvägledning
Här beskrivs hur du utformar program som är flexibla för fel i en distribuerad molnmiljö.   

* [Översikt över återhämtning][ResiliencyOvervew]
  
     Skapa program på Azure-plattformen som kan återställa från fel och fortsätta att fungera. Beskriver en strukturerad metod för att uppnå återhämtning, från design till implementering, distribution och åtgärder.
* [Checklista för återhämtning][resiliency-checklist]
  
    En checklista över rekommendationer som gör det enklare för dig att planera för en mängd olika fellägen som kan uppstå.
* [Analys av felläge][resiliency-fma] 
  
    Analys av programfel-läge (FMA) är en process för att skapa återhämtning i ett system genom att identifiera möjliga felpunkter. Den här artikeln innehåller en katalog med möjliga feltillstånd och åtgärder som en startpunkt för FMA-processen. 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md




<!--HONumber=Dec16_HO1-->



---
title: Händelseaggregering med Linux Azure Diagnostics
description: Lär dig mer om hur du samlar in och samlar in händelser med LAD för övervakning och diagnostik av Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: fdb78498d33416ef21b2e2b0f498e7afa6a58d99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609969"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Händelseaggregering och samling med Linux Azure Diagnostics
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

När du kör ett Azure Service Fabric-kluster är det en bra idé att samla in loggarna från alla noder på en central plats. Genom att ha loggarna på en central plats kan du analysera och felsöka problem i klustret eller problem i de program och tjänster som körs i klustret.

Ett sätt att ladda upp och samla in loggar är att använda TILLÄGGET Linux Azure Diagnostics (LAD), som överför loggar till Azure Storage, och även har möjlighet att skicka loggar till Azure Application Insights eller Event Hubs. Du kan också använda en extern process för att läsa händelser från lagring och placera dem i en analysplattformsprodukt, till exempel [Azure Monitor-loggar](../log-analytics/log-analytics-service-fabric.md) eller en annan loggtolkningslösning.

## <a name="log-and-event-sources"></a>Logg- och händelsekällor

### <a name="service-fabric-platform-events"></a>Plattformshändelser för Service Fabric
Service Fabric avger några färdiga loggar via [LTTng,](https://lttng.org)inklusive drifthändelser eller körningshändelser. Dessa loggar lagras på den plats som klustrets Resource Manager-mall anger. Om du vill hämta eller ange information om lagringskonto söker du efter taggen **AzureTableWinFabETWQueryable** och letar efter **StoreConnectionString**.

### <a name="application-events"></a>Programhändelser
 Händelser som avges från dina programs och tjänsternas kod enligt vad du angav när du instrumenterar programvaran. Du kan använda alla loggningslösningar som skriver textbaserade loggfiler , till exempel LTTng. Mer information finns i LTTng-dokumentationen för att spåra ditt program.

[Övervaka och diagnostisera tjänster i en lokal datorutvecklingsinställning](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>Distribuera diagnostiktillägget
Det första steget i att samla in loggar är att distribuera diagnostiktillägget på var och en av de virtuella datorerna i Service Fabric-klustret. Tillägget Diagnostik samlar in loggar på varje virtuell dator och överför dem till det lagringskonto som du anger. 

Om du vill distribuera diagnostiktillägget till de virtuella datorerna i **klustret** som en del av klusterskapande anger du Diagnostik till **På**. När du har skapat klustret kan du inte ändra den här inställningen med hjälp av portalen, så du måste göra lämpliga ändringar i Resource Manager-mallen.

Detta konfigurerar LAD-agenten för att övervaka angivna loggfiler. När en ny rad läggs till i filen skapas en syslog-post som skickas till lagringen (tabellen) som du har angett.


## <a name="next-steps"></a>Nästa steg

1. Mer information om vilka händelser du bör undersöka när du felsöker problem finns i [LTTng-dokumentation](https://lttng.org/docs) och [Använda LAD](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux).
2. [Konfigurera Log Analytics-agenten](service-fabric-diagnostics-event-analysis-oms.md) för att samla in mått, övervaka behållare som distribueras i klustret och visualisera loggarna 

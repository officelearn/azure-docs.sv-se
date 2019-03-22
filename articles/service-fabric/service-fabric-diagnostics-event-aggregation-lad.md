---
title: Azure Service Fabric händelse aggregering med Linux Azure-diagnostik | Microsoft Docs
description: Läs mer om sammanställa och samla in händelser med hjälp av LAD för övervakning och diagnostik för Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: ca6be2b08c9a38813b2c98eef7e5696bd61fd074
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57893041"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Händelsen aggregering och samling med Linux Azure-diagnostik
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

När du kör ett Azure Service Fabric-kluster, är det en bra idé att samla in loggar från alla noder i en central plats. Med loggarna på en central plats hjälper dig att analysera och felsöka problem i ditt kluster eller problem i program och tjänster som körs i klustret.

Ett sätt att överföra och samla in loggar är att använda Linux Azure Diagnostics (LAD)-tillägget, som laddar upp loggar till Azure Storage och har också möjlighet att skicka loggarna till Azure Application Insights eller Event Hubs. Du kan också använda en extern process för att läsa händelser från storage och placera dem i en plattform produkten analys som [Azure Monitor loggar](../log-analytics/log-analytics-service-fabric.md) eller en annan lösning för parsning av loggen.

## <a name="log-and-event-sources"></a>Logg- och händelsedata källor

### <a name="service-fabric-platform-events"></a>Service Fabric-plattformshändelser
Service Fabric genererar ett par out-of the box loggar via [LTTng](https://lttng.org), inklusive operativa händelser eller runtime-händelser. Dessa loggar lagras på den plats som anger klustrets Resource Manager-mall. Om du vill hämta eller ange uppgifterna för lagringskontot, Sök efter taggen **AzureTableWinFabETWQueryable** och leta efter **StoreConnectionString**.

### <a name="application-events"></a>Programhändelser
 Händelser som sänts ut från dina program och tjänster kod som angetts av dig när arrangera din programvara. Du kan använda valfri lösning för loggning som skriver textbaserade loggfiler, till exempel LTTng. Mer information finns i dokumentationen för LTTng på spårning av ditt program.

[Övervaka och diagnostisera tjänster i en inställning för utveckling av lokala dator](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>Distribuera Diagnostics-tillägg
Det första steget i att samla in loggar är att distribuera diagnostiktillägget på var och en av de virtuella datorerna i Service Fabric-klustret. Diagnostiktillägget samlar in loggar på varje virtuell dator och överför dem till det lagringskonto som du anger. 

Om du vill distribuera Diagnostics-tillägg till de virtuella datorerna i klustret som en del av klustret har skapats, ange **diagnostik** till **på**. När du har skapat klustret kan ändra du inte den här inställningen med hjälp av portalen så att du har att göra ändringarna i Resource Manager-mallen.

Den här koden konfigurerar LAD agenten för att övervaka angivna loggfiler. När en ny rad läggs till i filen, skapas en syslog-post som skickas till lagringsutrymmet (tabell) som du har angett.


## <a name="next-steps"></a>Nästa steg

1. Mer i detalj om vilka händelser som ska undersökas vid felsökning av problem i [LTTng dokumentation](https://lttng.org/docs) och [med LAD](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux).
2. [Konfigurera Log Analytics-agenten](service-fabric-diagnostics-event-analysis-oms.md) för att samla in mått, övervaka behållare som distribueras i klustret och visualisera dina loggar 

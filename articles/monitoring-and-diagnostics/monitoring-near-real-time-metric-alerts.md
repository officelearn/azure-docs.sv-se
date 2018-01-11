---
title: "Nära realtid mått aviseringar i Azure-Monitor | Microsoft Docs"
description: "Nära realtid mått kan aviseringar du övervaka Azure-resurs så ofta som 1 minut."
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: cd1002929ad749ac1742e914a9f2411f09ec91d5
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2017
---
# <a name="near-real-time-metric-alerts-preview"></a>Nära realtid mått aviseringar (förhandsgranskning)
Azure-Monitor stöder nu en ny typ av mått aviseringar kallas nära realtid mått aviseringar (förhandsversion). Den här funktionen är för närvarande i förhandsversion.
Dessa aviseringar skiljer sig från vanlig mått aviseringar på några olika sätt

- **Förbättrad latens** -nära realtid mått kan aviseringar övervaka ändringar i måttvärden så snart som 1 minut.
- **Mer kontroll över mått villkor** -nära realtid mått aviseringar kan du definiera bättre Varningsregler. Aviseringarna nu stöd för övervakning maximala, minsta, genomsnittlig och totala värden mätvärden.
- **Kombinerade övervakning av flera mått** -nära realtid mått aviseringar kan du övervaka flera (för närvarande två) med en enskild regel. Varning aktiveras om båda mätvärdena bryta mot sina respektive tröskelvärden för den angivna tidsperioden.
- **Modulära meddelandesystem** - nära realtid mått aviseringar Använd [åtgärdsgrupper](monitoring-action-groups.md). Den här funktionen kan användare skapa åtgärder i en modulär sätt och återanvända för många Varningsregler.

> [!NOTE]
> Nära realtid mått aviseringar är för närvarande i förhandsversion. Funktioner och användarupplevelse kan ändras.
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>Vilka resurser kan skapa nära realtid mått aviseringar för?
Fullständig lista över resurstyper som stöds av nära realtid mått aviseringar:

* Microsoft.ApiManagement/service
* Microsoft.Automation/automationAccounts
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Storage/storageAccounts
* Microsoft.Storage/storageAccounts/services
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.CognitiveServices/accounts

## <a name="near-real-time-metric-alerts-on-metrics-with-dimensions"></a>Nära realtid mått aviseringar på mått med dimensioner
Stöder nära realtid mått aviseringar varnar vid mått med dimensioner. Dimensioner är ett sätt att filtrera dina mått för rätt nivå. Nära realtid mått stöds aviseringar om mått med dimensioner för följande typer av resurser

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (stöds bara för storage-konton i USA regioner)
* Microsoft.Storage/storageAccounts/services (stöds bara för storage-konton i USA regioner)


## <a name="create-a-near-real-time-metric-alert"></a>Skapa en nära realtid mått
Nära realtid mått kan för närvarande aviseringar endast skapas via Azure-portalen. Stöd för att konfigurera nästan realtid mått aviseringar via PowerShell-kommandoradsgränssnittet (CLI) och REST-API för Azure-Monitor kommer snart.

Skapa aviseringar upplevelsen för nära realtid mått aviseringen har flyttats till den nya **Alerts(Preview)** upplevelse. Även om de aktuella aviseringarna sidan visar **lägga till nära realtid mått avisering**, du omdirigeras till den nya upplevelsen.

Du kan skapa en nära realtid mått med hjälp av stegen som beskrivs [här](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="managing-near-real-time-metric-alerts"></a>Hantera nära realtid mått aviseringar
När du har skapat en **nära realtid mått avisering**, den kan hanteras med hjälp av stegen som beskrivs [här](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="next-steps"></a>Nästa steg

* [Mer information om den nya upplevelsen aviseringar (förhandsgranskning)](monitoring-overview-unified-alerts.md)
* [Lär dig mer om loggen aviseringar i Azure aviseringar (förhandsgranskning)](monitor-alerts-unified-log.md)
* [Lär dig mer om aviseringar i Azure](monitoring-overview-alerts.md)
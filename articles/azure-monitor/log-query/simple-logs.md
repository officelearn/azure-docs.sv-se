---
title: Enkel loggupplevelse i Azure Monitor (förhandsversion) | Microsoft-dokument
description: Med upplevelsen enkla loggar kan du skapa grundläggande frågor i Azure Monitor utan att interagera direkt med KQL.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 59bcb42edaf7d46498a3514b4f1c919c6e8cc0c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660265"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Enkel loggningsupplevelse i Azure Monitor (förhandsversion)
Azure Monitor ger en [omfattande upplevelse](get-started-portal.md) för att skapa [loggfrågor](log-query-overview.md) med KQL-språket. Du kanske inte kräver den fulla kraften i KQL dock och föredrar en förenklad upplevelse för grundläggande frågekrav. Med upplevelsen enkla loggar kan du skapa grundläggande frågor utan att interagera direkt med KQL. Du kan också använda enkla loggar som ett inlärningsverktyg för KQL eftersom du behöver mer sofistikerade frågor.

> [!NOTE]
> Enkla loggar implementeras för närvarande som ett test för endast Cosmos DB och Key Vaults. Vänligen dela dina erfarenheter med Microsoft via [User Voice](https://feedback.azure.com/forums/913690-azure-monitor) för att hjälpa oss att avgöra om vi kommer att expandera och släppa den här funktionen.


## <a name="scope"></a>Omfång
Med hjälp av enkla loggar hämtas data från tabellen *AzureDiagnostics,* *AzureMetrics*och *AzureActivity* för den valda resursen. 

## <a name="using-simple-logs"></a>Använda enkla loggar
Navigera till alla Cosmos DB eller Key Vault i din Azure-prenumeration med [diagnostikinställningar konfigurerade för att samla in loggar i en Log Analytics-arbetsyta](../platform/resource-logs-collect-storage.md). Klicka på Loggar på **övervakningsmenyn** för att öppna upplevelsen enkla loggar. **Monitoring**

![Meny](media/simple-logs/menu.png)

Välj ett **fält** och en **operator** och ange ett **värde** för jämförelse. Klicka **+** på och ange **Och/Eller** om du vill lägga till ytterligare villkor.

![Kriterie](media/simple-logs/criteria.png)

Klicka på **Kör** om du vill visa frågeresultaten.

## <a name="view-and-edit-kql"></a>Visa och redigera KQL
Välj **Frågeredigeraren** om du vill öppna KQL som genereras av frågan Enkla loggar i hela Log Analytics-upplevelsen. 

![Frågeredigeraren](media/simple-logs/query-editor.png)

Du kan redigera KQL direkt och använda andra funktioner i Logganalys, till exempel filter för att ytterligare förfina dina resultat.

![Redigera KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Nästa steg

- Slutför en självstudiekurs om [hur du använder Logganalys i Azure-portalen](get-started-portal.md).
- Fyll i en handledning om [hur du skriver loggfrågor](get-started-portal.md).

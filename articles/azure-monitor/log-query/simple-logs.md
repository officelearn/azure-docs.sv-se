---
title: Enkla loggar i Azure Monitor (för hands version) | Microsoft Docs
description: Med enkel inloggnings upplevelsen kan du skapa grundläggande frågor i Azure Monitor utan att direkt interagera med KQL.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 4926e18aa6b00fe36608843ea5253903ace774e2
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92929310"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Enkla loggar i Azure Monitor (för hands version)
Azure Monitor ger en [rikare upplevelse](get-started-portal.md) för att skapa [logg frågor](log-query-overview.md) med KQL-språket. Du behöver inte ha full effekt på KQL, men du föredrar en förenklad upplevelse för grundläggande krav på frågor. Med enkel inloggnings upplevelsen kan du skapa grundläggande frågor utan att direkt interagera med KQL. Du kan också använda enkla loggar som ett utbildnings verktyg för KQL eftersom du behöver mer avancerade frågor.

> [!NOTE]
> Enkla loggar är för närvarande implementerade som ett test för endast Cosmos DB och nyckel valv. Dela din upplevelse med Microsoft via [användar rösten](https://feedback.azure.com/forums/913690-azure-monitor) för att hjälpa oss att avgöra om vi ska expandera och lansera den här funktionen.


## <a name="scope"></a>Omfång
De enkla loggarna hämtar data från *AzureDiagnostics* -, *AzureMetrics* -och *AzureActivity* -tabellen för den valda resursen. 

## <a name="using-simple-logs"></a>Använda enkla loggar
Navigera till en Cosmos DB eller Key Vault i din Azure-prenumeration med [diagnostikinställningar som kon figurer ATS för att samla in loggar i en Log Analytics arbets yta](../platform/resource-logs.md#send-to-azure-storage). Klicka på **loggar** på menyn **övervakning** för att öppna de enkla loggarna.

![Skärm bild som visar menyn övervakare med loggar valt.](media/simple-logs/menu.png)

Välj ett **fält** och en **operator** och ange ett **värde** för jämförelse. Klicka **+** och ange **och/eller** om du vill lägga till ytterligare kriterier.

![Skärm bild som visar sökningen i fönstret loggar med enkla loggar markerade.](media/simple-logs/criteria.png)

Klicka på **Kör** för att visa resultatet av frågan.

## <a name="view-and-edit-kql"></a>Visa och redigera KQL
Välj **Frågeredigeraren** för att öppna KQL som genererats av frågan enkla loggar i den fullständiga log Analyticss upplevelsen. 

![Frågeredigeraren](media/simple-logs/query-editor.png)

Du kan redigera KQL direkt och använda andra funktioner i Log Analytics, till exempel filter för att ytterligare förfina resultaten.

![Redigera KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Nästa steg

- Slutför en själv studie kurs om hur [du använder Log Analytics i Azure Portal](get-started-portal.md).
- Slutför en själv studie kurs om hur du [skriver logg frågor](get-started-portal.md).

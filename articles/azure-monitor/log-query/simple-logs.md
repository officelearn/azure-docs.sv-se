---
title: Enkla loggar i Azure Monitor (för hands version) | Microsoft Docs
description: Med enkel inloggnings upplevelsen kan du skapa grundläggande frågor i Azure Monitor utan att direkt interagera med KQL.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: bwren
ms.openlocfilehash: bff80b7083f2fb2e35af7db652849d527a5b8ba4
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963452"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Enkla loggar i Azure Monitor (för hands version)
Azure Monitor ger en [rikare upplevelse](get-started-portal.md) för att skapa [logg frågor](log-query-overview.md) med KQL-språket. Du behöver inte ha full effekt på KQL, men du föredrar en förenklad upplevelse för grundläggande krav på frågor. Med enkel inloggnings upplevelsen kan du skapa grundläggande frågor utan att direkt interagera med KQL. Du kan också använda enkla loggar som ett utbildnings verktyg för KQL eftersom du behöver mer avancerade frågor.

> [!NOTE]
> Enkla loggar är för närvarande implementerade som ett test för endast Cosmos DB och nyckel valv. Dela din upplevelse med Microsoft via [användar rösten](https://feedback.azure.com/forums/913690-azure-monitor) för att hjälpa oss att avgöra om vi ska expandera och lansera den här funktionen.


## <a name="scope"></a>Omfång
De enkla loggarna hämtar data från *AzureDiagnostics*-, *AzureMetrics*-och *AzureActivity* -tabellen för den valda resursen. 

## <a name="using-simple-logs"></a>Använda enkla loggar
Navigera till en Cosmos DB eller Key Vault i din Azure-prenumeration med [diagnostikinställningar som kon figurer ATS för att samla in loggar i en Log Analytics arbets yta](../platform/diagnostic-logs-stream-log-store.md). Klicka på **loggar** på menyn **övervakning** för att öppna de enkla loggarna.

![Meny](media/simple-logs/menu.png)

Välj ett **fält** och en **operator** och ange ett **värde** för jämförelse. Klicka **+** och ange **och/eller** om du vill lägga till ytterligare kriterier.

![Villkor](media/simple-logs/criteria.png)

Klicka på **Kör** för att visa resultatet av frågan.

## <a name="view-and-edit-kql"></a>Visa och redigera KQL
Välj **Frågeredigeraren** för att öppna KQL som genererats av frågan enkla loggar i den fullständiga log Analyticss upplevelsen. 

![Frågeredigeraren](media/simple-logs/query-editor.png)

Du kan redigera KQL direkt och använda andra funktioner i Log Analytics, till exempel filter för att ytterligare förfina resultaten.

![Redigera KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Nästa steg

- Slutför en själv studie kurs om hur [du använder Log Analytics i Azure Portal](get-started-portal.md).
- Slutför en själv studie kurs om hur du [skriver logg frågor](get-started-portal.md).

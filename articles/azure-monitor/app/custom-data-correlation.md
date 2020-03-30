---
title: Insikter för Azure-program | Microsoft-dokument
description: Korrelera data från Application Insights till andra datauppsättningar, till exempel dataanriknings- eller uppslagstabeller, datakällor som inte är application insights och anpassade data.
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 121e4699bd6a72f6865d3a6ffdef58c1b3806047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082769"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Korrelera Application Insights-data med anpassade datakällor

Application Insights samlar in flera olika datatyper: undantag, spårningar, sidvisningar och andra. Detta är ofta tillräckligt för att undersöka programmets prestanda, tillförlitlighet och användning, men det finns fall där det är användbart att korrelera data som lagras i Application Insights till andra helt anpassade datauppsättningar.

Några situationer där du kanske vill ha anpassade data är:

- Dataanrikning eller uppslagstabeller: till exempel komplettera ett servernamn med serverns ägare och den labbplats där den finns 
- Korrelation med datakällor som inte är application insights: korrelera till exempel data om ett köp i ett weblager med information från din inköpsuppfyllelsetjänst för att avgöra hur exakta dina uppskattningar av leveranstid var 
- Helt anpassade data: många av våra kunder älskar frågespråket och prestanda för Azure Monitor-loggplattformen som stöder Application Insights och vill använda den för att fråga data som inte alls är relaterade till Application Insights. Till exempel, för att spåra solpanel prestanda som en del av ett smart hem installation som beskrivs [här](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Så här korrelerar du anpassade data med Application Insights-data 

Eftersom Application Insights backas upp av den kraftfulla Azure Monitor-loggplattformen kan vi använda den fulla kraften i Azure Monitor för att använda data. Sedan skriver vi frågor med operatorn "join" som korrelerar dessa anpassade data med de data som är tillgängliga för oss i Azure Monitor-loggar. 

## <a name="ingesting-data"></a>Mata in data

I det här avsnittet granskar vi hur du hämtar dina data i Azure Monitor-loggar.

Om du inte redan har en, etablera en ny Log Analytics arbetsyta genom att följa [dessa instruktioner](../learn/quick-collect-azurevm.md) genom och inkludera steget "Skapa en arbetsyta".

Så här börjar du skicka loggdata till Azure Monitor. Det finns flera alternativ:

- För en synkron mekanism kan du antingen direkt anropa [datainsamlare-API:et](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) eller använda vår Logic App-anslutningsapp – leta bara efter "Azure Log Analytics" och välja alternativet "Skicka data":

  ![Skärmbild välja och åtgärder](./media/custom-data-correlation/01-logic-app-connector.png)  

- För ett asynkront alternativ använder du DATAInsamlare API för att skapa en bearbetningspipeline. Mer information finns i [den här artikeln.](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api)

## <a name="correlating-data"></a>Korrelera data

Application Insights baseras på Azure Monitor-loggplattformen. Vi kan därför använda kopplingar mellan resurser för att korrelera alla data som vi har intagit i Azure Monitor med våra Application [Insights-data.](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search)

Vi kan till exempel inta vårt labbinventering och platser i en tabell som heter "LabLocations_CL" i en Log Analytics-arbetsyta som kallas "myLA". Om vi sedan ville granska våra begäranden som spåras i Application Insights-appen "myAI" och korrelera de maskinnamn som serverade begärandena till platserna för dessa datorer som lagrats i den tidigare nämnda anpassade tabellen, kan vi köra följande fråga från antingen application insights eller Azure Monitor-kontexten:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Efterföljande moment

- Kolla in [API-referensen för datainsamlaren.](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)
- Mer information om [kopplingar mellan resurser](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search)finns .

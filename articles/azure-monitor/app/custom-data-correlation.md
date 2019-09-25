---
title: Azure Application Insights | Microsoft Docs
description: ''
services: application-insights
documentationcenter: ''
author: eternovsky
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.author: evternov
ms.openlocfilehash: 3abf50b3467ce9a97f2163a10ad1782f6e1c9c20
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258443"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Korrelera Application Insights data med anpassade data källor

Application Insights samlar in flera olika data typer: undantag, spår, sid visningar och andra. Det här är ofta tillräckligt för att undersöka programmets prestanda, tillförlitlighet och användning, men det finns fall då det är användbart att korrelera data som lagras i Application Insights till andra helt anpassade data uppsättningar.

Några situationer där du kanske vill att anpassade data ska vara:

- Data anriknings-eller uppslags tabeller: du kan till exempel komplettera ett server namn med ägaren av servern och den labb plats där den finns 
- Korrelation med icke-Application Insights data Källor: du kan t. ex. korrelera data om ett köp i en webb butik med information från din köp tjänst för att fastställa hur korrekta uppskattningar för leverans tiden är 
- Fullständigt anpassade data: många av våra kunder älskar att älska frågespråket och prestanda för den Azure Monitor logg plattform som säkerhetskopierar Application Insights och vill använda den för att fråga efter data som inte är relaterade till Application Insights. Till exempel för att spåra sol skärms prestanda som en del av en Smart Start installation som beskrivs [här](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Korrelera anpassade data med Application Insights data 

Eftersom Application Insights backas upp av den kraftfulla Azure Monitor logg plattformen kan vi använda den fullt kraften i Azure Monitor för att mata in data. Sedan ska vi skriva frågor med operatorn "Join" som korrelerar dessa anpassade data med de data som är tillgängliga för oss i Azure Monitor loggar. 

## <a name="ingesting-data"></a>Mata in data

I det här avsnittet ska vi granska hur du hämtar dina data till Azure Monitor loggar.

Om du inte redan har ett kan du etablera en ny Log Analytics arbets yta genom att följa [dessa instruktioner](../learn/quick-collect-azurevm.md) genom att följa steget "skapa en arbets yta".

För att börja skicka loggdata till Azure Monitor. Det finns flera alternativ:

- För en synkron mekanism kan du antingen anropa [API: et för data insamlaren](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) direkt eller använda vår Logic app Connector – du behöver bara leta efter "Azure Log Analytics" och välja alternativet "skicka data":

  ![Skärm bilds val och åtgärd](./media/custom-data-correlation/01-logic-app-connector.png)  

- För ett asynkront alternativ använder du API: et för data insamling för att bygga en bearbetnings pipeline. Mer information finns i [den här artikeln](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) .

## <a name="correlating-data"></a>Korrelera data

Application Insights baseras på Azure Monitor logg plattform. Vi kan därför använda [kors resurs kopplingar](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) för att korrelera data som vi har matat in i Azure monitor med våra Application Insights data.

Vi kan till exempel mata in vår labb inventering och platser i en tabell med namnet "LabLocations_CL" på en Log Analytics arbets yta med namnet "myLA". Om vi sedan ville granska våra begär Anden som spårats i Application Insights app med namnet "myAI" och korrelera dator namnen som hanterade begär anden till platserna för de här datorerna som lagras i den tidigare nämnda anpassade tabellen, kan vi köra följande fråga från antingen Application Insights-eller Azure Monitors kontexten:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Nästa steg

- Kolla in API-referens för [data insamling](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) .
- För mer information om [anslutningar över flera resurser](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

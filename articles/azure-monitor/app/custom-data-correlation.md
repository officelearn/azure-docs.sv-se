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
ms.author: Evgeny.Ternovsky
ms.openlocfilehash: b7814ce2ae94216da691b9a54049d20a03aafdd9
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55994824"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Korrelera Application Insights-data med anpassade datakällor

Application Insights samlar in flera olika datatyper: undantag, spår, sidvisningar och andra. Det här är ofta räcker för att undersöka ditt programs prestanda, tillförlitlighet och användning, men det finns fall när det är bra att korrelera data som lagras i Application Insights till andra helt anpassad datauppsättningar.

Vissa situationer där du anpassade data är:

- Berikande eller -uppslag datatabeller: till exempel kompletterar ett servernamn med ägaren av servern och lab platsen där den finns 
- Korrelationen med Application Insights-datakällor: till exempel korrelera data om ett inköp på ett webb-Arkiv med information från ditt köp betjäna tjänsten att avgöra hur exakt din leveranstiden beräknar har 
- Helt anpassade data: många av våra kunder älskar frågespråk och prestanda för Azure Monitor log-plattformen som säkerhetskopierar Application Insights och vill använda den för att köra frågor mot data som inte är relaterad till Application Insights. Till exempel att spåra solpaneler prestanda som en del av en smart home installation som beskrivs [här]( https://blogs.catapultsystems.com/cfuller/archive/2017/10/04/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Så här att korrelera anpassade data med Application Insights-data 

Eftersom Application Insights backas upp av den kraftfulla Azure Monitor log-plattformen, kommer du att använda hela kraften hos Azure Monitor för att mata in data. Sedan skriver du frågor med operatorn ”gå med” som kommer den här anpassade data med data som är tillgängliga för oss i Azure Monitor-loggar. 

## <a name="ingesting-data"></a>Mata in data

I det här avsnittet kommer vi att granska hur du hämtar dina data till Azure Monitor-loggar.

Om du inte redan har ett kan du etablera en ny Log Analytics-arbetsyta genom att följa [instruktionerna](../learn/quick-collect-azurevm.md) till och med steget ”Skapa en arbetsyta”.

Att börja skicka loggdata i Azure Monitor. Det finns flera alternativ:

- En synkron metod du kan antingen direkt kalla den [API för datainsamling](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) eller använda vår Logic App-anslutningsapp – helt enkelt leta efter ”Azure Log Analytics” och välj alternativet ”Skicka Data”:

 ![Välj skärmbild och åtgärd](./media/custom-data-correlation/01-logic-app-connector.png)  

- För en asynkron alternativet att använda Data Collector API för att skapa en process-pipelinen. Se [i den här artikeln](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) information.

## <a name="correlating-data"></a>Korrelera data

Application Insights bygger på Azure Monitor log-plattformen. Därför kan vi använda [mellan resurser kopplingar](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) att korrelera data som vi matas in i Azure Monitor med våra Application Insights-data.

Exempelvis kan vi mata in vår lab inventerings- och platser till en tabell med namnet ”LabLocations_CL” i en Log Analytics-arbetsyta som kallas ”myLA”. Om vi ville sedan granska våra förfrågningar som spåras i Application Insights-app som kallas ”myAI” och korrelera datornamnen som betjänas begäranden för dessa datorer som lagras i tabellen tidigare nämnda anpassade, kan vi köra följande fråga från den Application Insights eller Azure Monitor kontext:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Nästa steg

- Kolla in den [Data Collector API](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) referens.
- Mer information om [mellan resurser kopplingar](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

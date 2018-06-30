---
title: Azure Log Analytics query language fusklapp | Microsoft Docs
description: Den här artikeln innehåller hjälp om övergång till nya frågespråket för Log Analytics om du redan är bekant med det äldre språket.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: bwren
ms.component: na
ms.openlocfilehash: 7c2158d8e6f64c7c356ba40b3bf56684f00cb8c0
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133038"
---
# <a name="transitioning-to-azure-log-analytics-new-query-language"></a>Övergång till nya Azure Log Analytics-frågespråket
Logganalys nyligen implementerat ett nytt frågespråk.  Den här artikeln innehåller hjälp om övergång till det här språket för Log Analytics om du redan är bekant med det äldre språket och fortfarande behöver hjälp.

## <a name="resources"></a>Resurser


## <a name="language-converter"></a>Språk konverterare

Om du är bekant med det äldre Log Analytics-frågespråket är det enklaste sättet att skapa samma fråga på nytt språk konverteraren språk som har installerats i loggen Sök portal när din arbetsyta konverteras.  Konverteraren är lika enkelt som att skriva i en äldre fråga i den översta textrutan och klicka sedan på **konvertera**.  Du kan antingen klicka på Sök för att köra frågan eller kopiera och klistra in den för att använda den någon annanstans.

![Språk konverterare](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="resources"></a>Resurser
Den [dokumentationswebbplats för Log Analytics-frågespråket](https://docs.loganalytics.io) har alla de resurser som du behöver för att komma igång på det nya språket.  Detta inkluderar självstudier, exempel och en fullständig Språkreferens.


## <a name="cheat-sheet"></a>Översiktsblad

Följande tabell innehåller en jämförelse mellan olika vanliga frågor till motsvarande kommandon mellan nya och gamla frågespråket i Azure logganalys.

| Beskrivning | Bakåtkompatibelt | nytt |
|:--|:--|:--|
| Sök alla tabeller      | fel | Sök ”error” (inte skiftlägeskänsliga) |
| Välj data från tabellen | Typ = händelse |  Händelse |
|                        | Typ = händelse &#124; Välj källa, EventLog, händelse-ID | Händelsen &#124; projektet källa, EventLog, händelse-ID |
|                        | Typ = händelse &#124; uppifrån 100 | Händelsen &#124; ta 100 |
| Strängjämförelse      | Typ = händelsen Computer=srv01.contoso.com   | Händelsen &#124; där datorn == ”srv01.contoso.com” |
|                        | Typ = händelsen Computer=contains("contoso") | Händelsen &#124; där datorn innehåller ”contoso” (inte skiftlägeskänsliga)<br>Händelsen &#124; där datorn contains_cs ”Contoso” (skiftlägeskänslig) |
|                        | Typ = händelse datorn = RegEx (”\@contoso @”)  | Händelsen &#124; där datorn matchar regex ”. *contoso*” |
| Jämförelse av datum        | Typ av händelse TimeGenerated = > nu 1DAYS | Händelsen &#124; där TimeGenerated > ago(1d) |
|                        | Typ av händelse TimeGenerated = > 2017-05-01 TimeGenerated < 2017-05-31 | Händelsen &#124; där TimeGenerated mellan (datetime(2017-05-01)... datetime(2017-05-31)) |
| Booleskt jämförelse     | Typ = pulsslag IsGatewayInstalled = false  | Pulsslag \| där IsGatewayInstalled == false |
| Sortera                   | Typ = händelse &#124; sortera datorn asc, EventLog desc, EventLevelName asc | Händelsen \| sortera efter dator asc, EventLog desc, EventLevelName asc |
| Distinkta               | Typ = händelse &#124; dedupliceringen datorn \| Välj dator | Händelsen &#124; sammanfatta per dator, EventLog |
| Utöka kolumner         | Typ = Perf CounterName = ”% processortid” &#124; utöka if(map(CounterValue,0,50,0,1),"HIGH","LOW") som användning | Perf &#124; där CounterName == ”% processortid” \| utöka användningen = iff (CounterValue > 50, ”hög”, ”lågt”) |
| Sammansättning            | Typ = händelse &#124; mäta count() som antal per dator | Händelsen &#124; sammanfatta Count = count() per dator |
|                                | Typ = Perf ObjectName = Processor CounterName = ”% processortid” &#124; mäta avg(CounterValue) datorn intervall 5 minut | Perf &#124; där ObjectName == ”-Processor” och CounterName == ”% processortid” &#124; sammanfatta avg(CounterValue) per dator, bin (TimeGenerated 5 minuter) |
| Aggregeringen med gräns | Typ = händelse &#124; mäta count() per dator &#124; topp 10 | Händelsen &#124; sammanfatta AggregatedValue = count() per dator &#124; begränsa 10 |
| Union                  | Typ = händelse eller typ = Syslog | Union händelse Syslog |
| Slå ihop                   | Typ = NetworkMonitoring &#124; ansluta inre AgentIP (typ = pulsslag) ComputerIP | NetworkMonitoring &#124; ansluta typ = internt (Sök typen == ”pulsslag”) på $left. AgentIP == $right.ComputerIP |



## <a name="next-steps"></a>Nästa steg
- Checka ut en [självstudier om hur du skriver frågor](https://go.microsoft.com/fwlink/?linkid=856078) med det nya språket i fråga.
- Referera till den [Språkreferens för frågan](https://go.microsoft.com/fwlink/?linkid=856079) detaljer om alla kommandot operatorer och funktioner för det nya språket i fråga.  

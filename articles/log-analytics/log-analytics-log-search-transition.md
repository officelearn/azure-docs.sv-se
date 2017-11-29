---
title: Azure Log Analytics query language fusklapp | Microsoft Docs
description: "Den här artikeln innehåller hjälp om övergång till nya frågespråket för Log Analytics om du redan är bekant med det äldre språket."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: bwren
ms.openlocfilehash: 9c487ab33859ae453a0074ef0344f61de19c7b4d
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="transitioning-to-azure-log-analytics-new-query-language"></a>Övergång till nya Azure Log Analytics-frågespråket
Logganalys nyligen implementerat ett nytt frågespråk.  Den här artikeln innehåller hjälp om övergång till det här språket för Log Analytics om du redan är bekant med det äldre språket och fortfarande behöver hjälp.

## <a name="resources"></a>Resurser


## <a name="language-converter"></a>Språk konverterare

Om du är bekant med det äldre Log Analytics-frågespråket är det enklaste sättet att skapa samma fråga på nytt språk konverteraren språk som har installerats i loggen Sök portal när din arbetsyta konverteras.  Konverteraren är lika enkelt som att skriva i en äldre fråga i den översta textrutan och klicka sedan på **konvertera**.  Du kan antingen klicka på Sök för att köra frågan eller kopiera och klistra in den för att använda den någon annanstans.

![Språk konverterare](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="resources"></a>Resurser
Den [dokumentationswebbplats för Log Analytics-frågespråket](https://docs.loganalytics.io) har alla de resurser som du behöver för att komma igång på det nya språket.  Detta inkluderar självstudier, exempel och en fullständig Språkreferens.


## <a name="cheat-sheet"></a>Fusklapp

Följande tabell innehåller en jämförelse mellan olika vanliga frågor till motsvarande kommandon mellan nya och gamla frågespråket i Azure logganalys.

| Beskrivning | Äldre | Ny |
|:--|:--|:--|
| Sök alla tabeller      | fel | Sök ”error” (inte skiftlägeskänsliga) |
| Välj data från tabellen | Typ = händelse |  Händelse |
|                        | Typ = händelse &#124; Välj källa, EventLog, händelse-ID | Händelsen &#124; projektet källa, EventLog, händelse-ID |
|                        | Typ = händelse &#124; de 100 främsta | Händelsen &#124; ta 100 |
| Strängjämförelse      | Typ = händelsen Computer=srv01.contoso.com   | Händelsen &#124; Om datorn == ”srv01.contoso.com” |
|                        | Typ = händelsen Computer=contains("contoso") | Händelsen &#124; Om datorn innehåller ”contoso” (inte skiftlägeskänsliga)<br>Händelsen &#124; Om datorn contains_cs ”Contoso” (skiftlägeskänslig) |
|                        | Typ = händelse datorn = RegEx (”@contoso@”)  | Händelsen &#124; Om datorn matchar regex ”. *contoso*” |
| Jämförelse av datum        | Typ av händelse TimeGenerated = > nu 1DAYS | Händelsen &#124; där TimeGenerated > ago(1d) |
|                        | Typ av händelse TimeGenerated = > 2017-05-01 TimeGenerated < 2017-05-31 | Händelsen &#124; där TimeGenerated mellan (datetime(2017-05-01)... datetime(2017-05-31)) |
| Booleskt jämförelse     | Typ = pulsslag IsGatewayInstalled = false  | Pulsslag \| där IsGatewayInstalled == false |
| Sortera                   | Typ = händelse &#124; Sortera datorn asc, EventLog desc, EventLevelName asc | Händelsen \| Sortera efter dator asc, EventLog desc, EventLevelName asc |
| Distinkta               | Typ = händelse &#124; dedupliceringen datorn \| Välj dator | Händelsen &#124; Sammanfatta per dator, EventLog |
| Utöka kolumner         | Typ = Perf CounterName = ”% processortid” &#124; Utöka if(map(CounterValue,0,50,0,1),"HIGH","LOW") som användning | Perf &#124; Om CounterName == ”% processortid” \| Utöka användningen = iff (CounterValue > 50, ”hög”, ”lågt”) |
| Aggregeringen            | Typ = händelse &#124; måttet count() som antal per dator | Händelsen &#124; Sammanfatta Count = count() per dator |
|                                | Typ = Perf ObjectName = Processor CounterName = ”% processortid” &#124; måttet avg(CounterValue) datorn intervall 5 minut | Perf &#124; där ObjectName == ”-Processor” och CounterName == ”% processortid” &#124; Sammanfatta avg(CounterValue) per dator, bin (TimeGenerated 5 minuter) |
| Aggregeringen med gräns | Typ = händelse &#124; måttet count() av datorn &#124; Topp 10 | Händelsen &#124; Sammanfatta AggregatedValue = count() av datorn &#124; begränsa 10 |
| Union                  | Typ = händelse eller typ = Syslog | Union händelse Syslog |
| Slå ihop                   | Typ = NetworkMonitoring &#124; Anslut inre AgentIP (typ = pulsslag) ComputerIP | NetworkMonitoring &#124; Anslut typ = internt (Sök typen == ”pulsslag”) på $left. AgentIP == $right.ComputerIP |



## <a name="next-steps"></a>Nästa steg
- Checka ut en [självstudier om hur du skriver frågor](https://go.microsoft.com/fwlink/?linkid=856078) med det nya språket i fråga.
- Referera till den [Språkreferens för frågan](https://go.microsoft.com/fwlink/?linkid=856079) detaljer om alla kommandot operatorer och funktioner för det nya språket i fråga.  

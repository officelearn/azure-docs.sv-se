---
title: Sökfrågor i Log Analytics | Microsoft Docs
description: Den här artikeln innehåller en självstudie för att komma igång skriva sökfrågor i Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/06/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 2ccef960378190f10e64318f91039871657a1a46
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603761"
---
# <a name="search-queries-in-log-analytics"></a>Sökfrågor i Log Analytics

> [!NOTE]
> Bör du genomföra [Kom igång med frågor i Log Analytics](get-started-queries.md) innan du slutför den här lektionen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Azure Log Analytics-frågor kan börja med ett tabellnamn eller ett sökkommando. Den här självstudien tar upp search-baserade frågor. Det finns fördelar med att varje metod.

Tabell-baserade frågor starta genom att ange omfång frågan och därmed tenderar att vara effektivare än sökfrågor. Sökfrågor är mindre strukturerade vilket gör dem ett bättre val när du söker efter ett specifikt värde över kolumner eller tabeller. **Sök** söka igenom alla kolumner i en viss tabell eller i alla tabeller för det angivna värdet. Mängden data som bearbetas kan vara enorma, vilket är anledningen till de här frågorna kan ta längre tid att slutföra och kan returnera mycket stora resultatmängder.

## <a name="search-a-term"></a>Sök efter en term
Den **search** normalt används för att söka efter en viss tidsperiod. Alla kolumner i alla tabeller genomsöks efter termen ”error” i exemplet nedan:

```KQL
search "error"
| take 100
```

När de inte är lätt att använda sökndexet frågor som den visade ovan är inte effektiva och är många irrelevanta sökresultatet. En bättre metod är att söka i den aktuella tabellen eller även en viss kolumn.

### <a name="table-scoping"></a>Tabellen omfång
Om du vill söka efter en term i en viss tabell, lägger du till `in (table-name)` precis efter den **search** operator:

```KQL
search in (Event) "error"
| take 100
```

eller i flera tabeller:
```KQL
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>Tabell- och kolumnen omfång
Som standard **search** utvärderas alla kolumner i datauppsättningen. Om du vill söka i bara en viss kolumn, använder du följande syntax:

```KQL
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> Om du använder `==` i stället för `:`, resultatet skulle innehålla poster där den *källa* kolumnen har det exakta värdet ”fel”, och i den här skiftlägeskänsligt. Med hjälp av ””: kommer inte innehåller poster där *källa* har värden som ”felkoden 404” eller ”Error”.

## <a name="case-sensitivity"></a>Skiftlägeskänslighet
Termen search är skiftlägeskänslig som standard, så söker ”dns” kan ge resultat som DNS-”,” dns ”eller” Dns ”. För att göra sökningen skiftlägeskänsliga, använda den `kind` alternativet:

```KQL
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>Använda jokertecken
Den **search** kommandot stöder jokertecken i början, slut eller mitten av en term.

Så här söker villkor som börjar med ”win”:
```KQL
search in (Event) "win*"
| take 100
```

Så här söker villkor som slutar med ”.com”:
```KQL
search in (Event) "*.com"
| take 100
```

Så här söker villkor som innehåller ”www”:
```KQL
search in (Event) "*www*"
| take 100
```

Sök efter villkor som börjar med ”corp” och slutar med ”.com”, till exempel ”corp.mydomain.com” ”

```KQL
search in (Event) "corp*.com"
| take 100
```

Du kan också få allt i en tabell med bara ett jokertecken: `search in (Event) *`, men den detsamma som att skriva bara `Event`.

> [!TIP]
> Du kan använda `search *` för att hämta varje kolumn från varje tabell, rekommenderar vi att du alltid begränsa dina frågor till specifika tabeller. Sökndexet frågor kan ta en stund att slutföra och kan returnera för många resultat.

## <a name="add-and--or-to-search-queries"></a>Lägg till *och* / *eller* att söka efter frågor
Använd **och** att söka efter poster som innehåller flera villkor:

```KQL
search in (Event) "error" and "register"
| take 100
```

Använd **eller** att hämta poster som innehåller minst ett av villkoren:

```KQL
search in (Event) "error" or "register"
| take 100
```

Om du har flera sökvillkor kan kombinera du dem i samma fråga med hjälp av parenteser:

```KQL
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

Resultatet av det här exemplet är poster som innehåller termen ”error” och även innehålla antingen ”registrera” eller något som börjar med ”konvertering”.

## <a name="pipe-search-queries"></a>Skicka sökfrågor
Precis som andra kommandon, **search** kan skickas så sökresultat kan filtreras, sorteras och aggregeras. Till exempel för att få en *händelse* poster som innehåller ”win”:

```KQL
search in (Event) "win"
| count
```




## <a name="next-steps"></a>Nästa steg

- Se ytterligare självstudier på den [Log Analytics query language plats](https://docs.loganalytics.io)
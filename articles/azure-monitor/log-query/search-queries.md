---
title: Sökfrågor i Azure Monitor-loggar | Microsoft-dokument
description: Den här artikeln innehåller en självstudiekurs för att komma igång med sökfunktionen i Azure Monitor-loggfrågor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/06/2018
ms.openlocfilehash: e13f4abc37e348759e7d0b8a2f7d890c82fe0d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660248"
---
# <a name="search-queries-in-azure-monitor-logs"></a>Sökfrågor i Azure Monitor-loggar
Azure Monitor-loggfrågor kan börja med antingen ett tabellnamn eller ett sökkommando. Den här självstudien täcker sökbaserade frågor. Det finns fördelar med varje metod.

Tabellbaserade frågor börjar med att omfånga frågan och tenderar därför att vara effektivare än sökfrågor. Sökfrågor är mindre strukturerade vilket gör dem till det bättre valet när de söker efter ett visst värde i kolumner eller tabeller. **sökningen** kan söka igenom alla kolumner i en viss tabell, eller i alla tabeller, efter det angivna värdet. Mängden data som bearbetas kan vara enorm, vilket är anledningen till att dessa frågor kan ta längre tid att slutföra och kan returnera mycket stora resultatuppsättningar.

## <a name="search-a-term"></a>Sök efter en term
**Sökkommandot** används vanligtvis för att söka efter en viss term. I följande exempel genomsöks alla kolumner i alla tabeller efter termen "fel":

```Kusto
search "error"
| take 100
```

Även om de är lätta att använda, unscoped frågor som den som visades ovan är inte effektiva och kommer sannolikt att returnera många irrelevanta resultat. En bättre praxis skulle vara att söka i den relevanta tabellen, eller till och med en viss kolumn.

### <a name="table-scoping"></a>Tabell scoping
Om du vill söka efter `in (table-name)` en term i en viss tabell lägger du till strax efter sökoperatorn: **search**

```Kusto
search in (Event) "error"
| take 100
```

eller i flera tabeller:
```Kusto
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>Tabell- och kolumnstfångst
Som standard utvärderas alla kolumner i datauppsättningen genom att **söka.** Om du bara vill söka i en viss kolumn (med namnet *Källa* i exemplet nedan) använder du den här syntaxen:

```Kusto
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> Om du `==` använder `:`i stället för innehåller resultaten poster där kolumnen *Källa* har det exakta värdet "fel" och i det här fallet. Med ":" ingår poster där *Source* har värden som "felkod 404" eller "Fel".

## <a name="case-sensitivity"></a>Skiftlägeskänslighet
Som standard är termsökning skiftlägesokänslig, så att söka "dns" kan ge resultat som "DNS", "dns" eller "Dns". Om du vill göra sökfallet `kind` känsligt använder du alternativet:

```Kusto
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>Använd wild cards
**Sökkommandot** stöder jokertecken, i början, slutet eller mitten av en term.

Så här söker du termer som börjar med "win":
```Kusto
search in (Event) "win*"
| take 100
```

Så här söker du termer som slutar med ".com":
```Kusto
search in (Event) "*.com"
| take 100
```

Så här söker du termer som innehåller "www":
```Kusto
search in (Event) "*www*"
| take 100
```

Så här söker du termer som börjar med "corp" och slutar på ".com", till exempel "corp.mydomain.com""

```Kusto
search in (Event) "corp*.com"
| take 100
```

Du kan också få allt i ett bord `search in (Event) *`genom att bara ett wild `Event`card: , men det skulle vara samma som att skriva bara .

> [!TIP]
> Du kan `search *` använda för att hämta varje kolumn från varje tabell, men vi rekommenderar att du alltid scoperar dina frågor till specifika tabeller. Det kan ta ett tag att slutföra unscoped-frågor och kan ge för många resultat.

## <a name="add-and--or-to-search-queries"></a>Lägga till *och* / *eller* söka efter frågor
Använda **och** söka efter poster som innehåller flera termer:

```Kusto
search in (Event) "error" and "register"
| take 100
```

Använda **eller** hämta poster som innehåller minst ett av villkoren:

```Kusto
search in (Event) "error" or "register"
| take 100
```

Om du har flera sökvillkor kan du kombinera dem till samma fråga med hjälp av parenteser:

```Kusto
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

Resultatet av det här exemplet skulle vara poster som innehåller termen "fel" och även innehåller antingen "register" eller något som börjar med "marskalk".

## <a name="pipe-search-queries"></a>Frågor om rörsökning
Precis som alla andra kommandon kan **sökningen** ledas så att sökresultaten kan filtreras, sorteras och aggregeras. Om du till exempel vill hämta antalet *händelseposter* som innehåller "win":

```Kusto
search in (Event) "win"
| count
```




## <a name="next-steps"></a>Nästa steg

- Se ytterligare självstudier på [Kusto-frågespråkswebbplatsen](/azure/kusto/query/).

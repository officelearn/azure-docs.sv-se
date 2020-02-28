---
title: Sök frågor i Azure Monitor loggar | Microsoft Docs
description: Den här artikeln innehåller en självstudie för att komma igång med sökning i Azure Monitor logg frågor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/06/2018
ms.openlocfilehash: e13f4abc37e348759e7d0b8a2f7d890c82fe0d15
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660248"
---
# <a name="search-queries-in-azure-monitor-logs"></a>Sök frågor i Azure Monitor loggar
Azure Monitors logg frågor kan inledas med antingen ett tabell namn eller ett Sök kommando. I den här självstudien beskrivs sökbaserade frågor. Det finns fördelar med varje metod.

Tabellbaserade frågor börjar genom att omfånget av frågan och därför tenderar att bli effektivare än Sök frågor. Sök frågor är mindre strukturerade och gör dem till bättre val när du söker efter ett bestämt värde i kolumner eller tabeller. **Sök** kan söka igenom alla kolumner i en viss tabell, eller i alla tabeller, för det angivna värdet. Mängden data som bearbetas kan vara enorma, vilket är orsaken till att dessa frågor kan ta längre tid att slutföra och kan returnera mycket stora resultat uppsättningar.

## <a name="search-a-term"></a>Sök efter en term
Kommandot **search** används vanligt vis för att söka efter en speciell term. I följande exempel genomsöks alla kolumner i alla tabeller efter termen "fel":

```Kusto
search "error"
| take 100
```

Även om de är enkla att använda, är det inte effektivt att använda begränsade frågor som de som visades ovan, och de kan förmodligen returnera många irrelevanta resultat. En bättre praxis är att söka i relevant tabell eller till och med en speciell kolumn.

### <a name="table-scoping"></a>Tabell omfattning
Om du vill söka efter en term i en speciell tabell lägger du till `in (table-name)` strax efter **Sök** operatorn:

```Kusto
search in (Event) "error"
| take 100
```

eller i flera tabeller:
```Kusto
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>Tabell-och kolumn omfång
Som standard utvärderas alla kolumner i data uppsättningen av **sökningen** . Om du bara vill söka i en speciell kolumn (namngiven *källa* i exemplet nedan) använder du följande syntax:

```Kusto
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> Om du använder `==` i stället för `:`, innehåller resultaten poster där *käll* kolumnen har det exakta värdet "Error" och i detta exakta fall. Om du använder ":" inkluderas poster där *källan* har värden som "Felkod 404" eller "fel".

## <a name="case-sensitivity"></a>Skift läges känslighet
Som standard är terms ökning Skift läges känsligt, så sökning efter DNS kan ge resultat som "DNS", "DNS" eller "DNS". Om du vill göra sökningen Skift läges känslig använder du alternativet `kind`:

```Kusto
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>Använd jokertecken
Kommandot **search** stöder jokertecken, i början, i slutet eller mitten av en term.

För att söka efter termer som börjar med "win":
```Kusto
search in (Event) "win*"
| take 100
```

För att söka efter termer som slutar med ". com":
```Kusto
search in (Event) "*.com"
| take 100
```

För att söka efter termer som innehåller "www":
```Kusto
search in (Event) "*www*"
| take 100
```

För att söka efter termer som börjar med "Corp" och slutar i ". com", t. ex. "corp.mydomain.com".

```Kusto
search in (Event) "corp*.com"
| take 100
```

Du kan också hämta allt i en tabell med bara ett jokertecken: `search in (Event) *`, men det är detsamma som att skriva `Event`.

> [!TIP]
> Även om du kan använda `search *` för att hämta alla kolumner från varje tabell, rekommenderar vi att du alltid omfångerar dina frågor till vissa tabeller. Det kan ta en stund att slutföra frågor som inte omfattas och kan returnera för många resultat.

## <a name="add-and--or-to-search-queries"></a>Lägg till *och* / *eller* för att söka efter frågor
Använd **och** för att söka efter poster som innehåller flera termer:

```Kusto
search in (Event) "error" and "register"
| take 100
```

Använda **eller** för att hämta poster som innehåller minst en av villkoren:

```Kusto
search in (Event) "error" or "register"
| take 100
```

Om du har flera Sök villkor kan du kombinera dem till samma fråga med hjälp av parenteser:

```Kusto
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

Resultatet av det här exemplet är poster som innehåller termen "fel" och som även innehåller antingen "Registrera" eller något som börjar med "Marshal".

## <a name="pipe-search-queries"></a>Sök frågor för pipe
Precis som andra kommandon kan **sökningen** vara skickas så att Sök resultaten kan filtreras, sorteras och aggregeras. Till exempel för att hämta antalet *händelse* poster som innehåller "win":

```Kusto
search in (Event) "win"
| count
```




## <a name="next-steps"></a>Nästa steg

- Se fler självstudier på [webbplatsen för Kusto-frågespråk](/azure/kusto/query/).

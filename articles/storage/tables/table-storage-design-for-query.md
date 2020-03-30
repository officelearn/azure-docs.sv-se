---
title: Designa Azure Table Storage för frågor | Microsoft-dokument
description: Designtabeller för frågor i Azure Table storage.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 41a588ddc0c1be8014a84d8fe181013d8566f68d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457640"
---
# <a name="design-for-querying"></a>Utforma för frågor
Tabelltjänstlösningar kan läsas intensivt, skriva intensivt eller en blandning av de två. Den här artikeln fokuserar på de saker du bör tänka på när du utformar tabelltjänsten för att stödja läsåtgärder effektivt. Vanligtvis är en design som stöder läsåtgärder effektivt också effektiv för skrivåtgärder. Det finns dock ytterligare överväganden att tänka på när man utformar för att stödja skrivåtgärder, som beskrivs i artikeln [Design för dataändring](table-storage-design-for-modification.md).

En bra utgångspunkt för att utforma din tabelltjänstlösning så att du kan läsa data effektivt är att fråga "Vilka frågor behöver mitt program köra för att hämta de data som behövs från tabelltjänsten?"  

> [!NOTE]
> Med table-tjänsten är det viktigt att få designen korrekt på framsidan eftersom det är svårt och dyrt att ändra den senare. I en relationsdatabas är det till exempel ofta möjligt att åtgärda prestandaproblem genom att helt enkelt lägga till index i en befintlig databas: det här är inte ett alternativ med tabelltjänsten.  
> 
> 

Det här avsnittet fokuserar på de viktigaste problem som du måste ta itu med när du utformar tabellerna för frågor. De ämnen som behandlas i det här avsnittet är:

* [Hur ditt val av PartitionKey och RowKey påverkar frågeprestanda](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Välja en lämplig PartitionKey](#choosing-an-appropriate-partitionkey)
* [Optimera frågor för tabelltjänsten](#optimizing-queries-for-the-table-service)
* [Sortera data i tabelltjänsten](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Hur ditt val av PartitionKey och RowKey påverkar frågeprestanda
Följande exempel förutsätter att tabelltjänsten lagrar medarbetarentiteter med följande struktur (de flesta av exemplen utelämnar egenskapen **Tidsstämpel** för tydlighet):  

| *Kolumnnamn* | *Datatyp* |
| --- | --- |
| **PartitionKey** (avdelningsnamn) |String |
| **RowKey** (medarbetar-ID) |String |
| **Förnamn** |String |
| **Efternamn** |String |
| **Ålder** |Integer |
| **Emailaddress** |String |

I artikeln [Azure Table Storage Overview](table-storage-overview.md) beskrivs några av de viktigaste funktionerna i Azure Table-tjänsten som har en direkt inverkan på utformningen av frågor. Dessa resulterar i följande allmänna riktlinjer för att utforma tabelltjänstfrågor. Observera att filtersyntaxen som används i exemplen nedan kommer från REST API:et för tabelltjänsten, mer information finns [i Fråga entiteter](https://docs.microsoft.com/rest/api/storageservices/Query-Entities).  

* En ***punktfråga*** är den mest effektiva sökningen som ska användas och rekommenderas att användas för uppslag eller uppslag med stora volymer som kräver lägsta svarstid. En sådan fråga kan använda indexen för att hitta en enskild enhet mycket effektivt genom att ange både **PartitionKey-** och **RowKey-värdena.** Till exempel: $filter=(PartitionKey eq 'Sales') och (RowKey eq '2')  
* Näst bäst är en ***områdesfråga*** som använder **PartitionKey** och filtrerar på ett intervall med **RowKey-värden** för att returnera mer än en entitet. **PartitionKey-värdet** identifierar en specifik partition och **RowKey-värdena** identifierar en delmängd av entiteterna i den partitionen. Till exempel: $filter=PartitionKey eq 'Sales' och RowKey ge 'S' och RowKey lt 'T'  
* Tredje bästa är en ***partitionsgenomsökning*** som använder **PartitionKey** och filtrerar på en annan icke-nyckelgenskap och som kan returnera mer än en entitet. **PartitionKey-värdet** identifierar en specifik partition och egenskapsvärdena väljer för en delmängd av entiteterna i partitionen. Till exempel: $filter=PartitionKey eq 'Sales' och LastName eq 'Smith'  
* En ***tabell genomsökning*** innehåller inte **PartitionKey** och är mycket ineffektiv eftersom den söker alla partitioner som utgör din tabell i sin tur för alla matchande entiteter. Det kommer att utföra en tabell genomsökning oavsett om ditt filter använder **RowKey**. Till exempel: $filter=Efternamn eq 'Jones'  
* Frågor som returnerar flera entiteter returnerar dem sorterade i **PartitionKey** och **RowKey** ordning. Om du vill undvika att tillgripa entiteterna i klienten väljer du en **RowKey** som definierar den vanligaste sorteringsordningen.  

Observera att om du använder ett "**eller**" för att ange ett filter baserat på **RowKey-värden** resulterar i en partitionsgenomsökning och behandlas inte som en intervallfråga. Därför bör du undvika frågor som använder filter som: $filter=PartitionKey eq 'Sales' och (RowKey eq '121' eller RowKey eq '322')  

Exempel på kod på klientsidan som använder storage-klientbiblioteket för att köra effektiva frågor finns i:  

* [Köra en punktfråga med lagringsklientbiblioteket](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Hämta flera entiteter med LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Projektion på serversidan](table-storage-design-patterns.md#server-side-projection)  

Exempel på kod på klientsidan som kan hantera flera entitetstyper som lagras i samma tabell finns i:  

* [Arbeta med heterogena entitetstyper](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Välja en lämplig PartitionKey
Ditt val av **PartitionKey** bör balansera behovet av att aktivera användningen av EGTs (för att säkerställa konsekvens) mot kravet att distribuera dina entiteter över flera partitioner (för att säkerställa en skalbar lösning).  

På en extrem kan du lagra alla dina entiteter i en enda partition, men det kan begränsa skalbarheten för din lösning och förhindra att tabelltjänsten kan belastningsutjämna begäranden. På den andra extrema kan du lagra en entitet per partition, vilket skulle vara mycket skalbart och som gör att tabelltjänsten kan belastningsbalansbegäranden, men som skulle hindra dig från att använda entitetsgruppstransaktioner.  

En idealisk **PartitionKey** är en som gör att du kan använda effektiva frågor och som har tillräckligt med partitioner för att säkerställa att din lösning är skalbar. Vanligtvis kommer du att upptäcka att dina entiteter kommer att ha en lämplig egenskap som distribuerar dina entiteter över tillräckliga partitioner.

> [!NOTE]
> I ett system som lagrar information om användare eller anställda kan UserID till exempel vara en bra PartitionKey. Du kan ha flera entiteter som använder ett visst UserID som partitionsnyckel. Varje entitet som lagrar data om en användare grupperas i en enda partition, så dessa entiteter är tillgängliga via entitetsgrupptransaktioner, samtidigt som de är mycket skalbara.
> 
> 

Det finns ytterligare överväganden i ditt val av **PartitionKey** som relaterar till hur du ska infoga, uppdatera och ta bort entiteter. Mer information finns i [Utforma tabeller för dataändring](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>Optimera frågor för tabelltjänsten
Tabelltjänsten indexerar automatiskt dina entiteter med hjälp av **partitionsnyckel-** och **RowKey-värdena** i ett enda grupperat index, därav anledningen till att punktfrågor är de mest effektiva att använda. Det finns dock inga andra index än index på det klustrade indexet på **PartitionKey** och **RowKey**.

Många designer måste uppfylla kraven för att aktivera uppslag av entiteter baserat på flera kriterier. Till exempel att hitta medarbetarentiteter baserat på e-post, medarbetar-ID eller efternamn. De mönster som beskrivs i [Tabelldesignmönster](table-storage-design-patterns.md) behandlar dessa typer av krav och beskriver olika sätt att arbeta runt det faktum att tabelltjänsten inte tillhandahåller sekundära index:  

* [Sekundärt indexmönster inom partitionen](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Lagra flera kopior av varje entitet med hjälp av olika **RowKey-värden** (i samma partition) för att möjliggöra snabba och effektiva sökninger och alternativa sorteringsorder med hjälp av olika **RowKey-värden.**  
* [Sekundärt indexmönster mellan partitioner](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Lagra flera kopior av varje entitet med olika **RowKey-värden** i separata partitioner eller i separata tabeller för att möjliggöra snabba och effektiva sökninger och alternativa sorteringsorder med hjälp av olika **RowKey-värden.**  
* [Indexentiteter Mönster](table-storage-design-patterns.md#index-entities-pattern) - Underhåll indexentiteter för att möjliggöra effektiva sökningar som returnerar listor över entiteter.  

## <a name="sorting-data-in-the-table-service"></a>Sortera data i tabelltjänsten
Tabelltjänsten returnerar entiteter sorterade i stigande ordning baserat på **PartitionKey** och sedan efter **RowKey**. Dessa tangenter är strängvärden och för att säkerställa att numeriska värden sorteras korrekt bör du konvertera dem till en fast längd och pad dem med nollor. Om till exempel det medarbetar-ID-värde som du använder som **RowKey** är ett heltalsvärde, bör du konvertera medarbetar-ID **123** till **00000123**.  

Många program har krav på att använda data sorterade i olika order: till exempel sortera medarbetare efter namn eller genom att ansluta datum. Följande mönster behandlar hur du växlar order för dina entiteter:  

* [Sekundärt indexmönster inom partitionen](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Lagra flera kopior av varje entitet med hjälp av olika RowKey-värden (i samma partition) för att möjliggöra snabba och effektiva sökninger och alternativa sorteringsorder med hjälp av olika RowKey-värden.  
* [Sekundärt indexmönster mellan partitioner](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Lagra flera kopior av varje entitet med olika RowKey-värden i separata partitioner i separata tabeller för att möjliggöra snabba och effektiva sökninger och alternativa sorteringsorder med hjälp av olika RowKey-värden.
* [Log tail pattern](table-storage-design-patterns.md#log-tail-pattern) - Hämta n-entiteterna som senast lades till i en partition med hjälp av ett *n* **RowKey-värde** som sorteras i omvänd datum och tidsordning.  

## <a name="next-steps"></a>Nästa steg

- [Mönster för tabelldesign](table-storage-design-patterns.md)
- [Modellera relationer](table-storage-design-modeling.md)
- [Kryptera tabelldata](table-storage-design-encrypt-data.md)
- [Utforma för dataändring](table-storage-design-for-modification.md)

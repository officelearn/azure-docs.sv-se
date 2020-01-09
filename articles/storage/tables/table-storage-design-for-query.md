---
title: Designa Azure Table Storage för frågor | Microsoft Docs
description: Design tabeller för frågor i Azure Table Storage.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 41a588ddc0c1be8014a84d8fe181013d8566f68d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457640"
---
# <a name="design-for-querying"></a>Utforma för frågor
Table service-lösningar kan läsas intensiva, Skriv intensiva eller en blandning av båda. Den här artikeln fokuserar på saker som du bör tänka på när du utformar Table service som stöder Läs åtgärder effektivt. Normalt är också en design som stöder läsåtgärder effektivt effektivt för skrivåtgärder. Det finns dock ytterligare saker att tänka på när du utformar för att stödja Skriv åtgärder, som beskrivs i artikeln [design för data ändringar](table-storage-design-for-modification.md).

En bra utgångspunkt för att utforma din lösning för tabellen så att du kan läsa data på ett effektivt sätt är att ställa ”vilka frågor mitt program måste köra för att hämta data som behövs från tabelltjänsten”?  

> [!NOTE]
> Med tabelltjänsten är det viktigt att hämta designen rätt direkt eftersom det är svårt och dyrt att ändra den senare. Till exempel i en relationsdatabas det går ofta att åtgärda prestandaproblem genom att lägga till index till en befintlig databas: Detta är inte ett alternativ med Table service.  
> 
> 

Det här avsnittet fokuserar på viktiga problem som du måste ta när du utformar dina tabeller för frågor. Ämnena i det här avsnittet är:

* [Hur ett PartitionKey och RowKey påverkar prestanda för frågor](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Välja en lämplig PartitionKey](#choosing-an-appropriate-partitionkey)
* [Optimera frågor för Table service](#optimizing-queries-for-the-table-service)
* [Sortera data i Table service](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Hur ett PartitionKey och RowKey påverkar prestanda för frågor
I följande exempel förutsätter tabelltjänsten lagrar medarbetare entiteter med följande struktur (de flesta av exemplen utelämna den **tidsstämpel** egenskapen för tydlighetens skull):  

| *Kolumnnamn* | *Datatyp* |
| --- | --- |
| **PartitionKey** (avdelningsnamn) |String |
| **RowKey** (anställnings-Id) |String |
| **Förnamn** |String |
| **Efternamn** |String |
| **Ålder** |Integer |
| **E-postadress** |String |

I artikeln [Översikt över Azure Table Storage](table-storage-overview.md) beskrivs några av de viktigaste funktionerna i Azure Table service som direkt påverkar utformningen av frågor. Dessa resultera i följande allmänna riktlinjer för att utforma Table service-frågor. Observera att den filter-syntax som används i exemplen nedan är från Table service REST API. mer information finns i [fråga om entiteter](https://docs.microsoft.com/rest/api/storageservices/Query-Entities).  

* En ***punkt fråga*** är den mest effektiva sökningen att använda och rekommenderas som ska användas för stora volymer sökningar eller sökningar som kräver lägsta svarstid. En sådan fråga kan använda indexen för att hitta en enskild entitet effektivt genom att ange både **PartitionKey** -och **RowKey** -värden. Till exempel: $filter = (PartitionKey eq ”försäljning”) och (RowKey eq ”2”)  
* Andra bäst är en ***intervallet fråga*** som använder den **PartitionKey** och filter på flera olika **RowKey** värden att returnera mer än en entitet. Den **PartitionKey** värdet identifierar en specifik partition och **RowKey** identifierar en delmängd av entiteter i partitionen. Till exempel: $filter = PartitionKey-eq ”försäljning och RowKey ge” och RowKey lt 'T'  
* Tredje bästa är en ***Partition skanna*** som använder den **PartitionKey** och filter på en annan icke-nyckelegenskapen och som kan returnera fler än en entitet. Den **PartitionKey** värdet identifierar en specifik partition och egenskapen värden väljer du för en delmängd av entiteter i partitionen. Till exempel: $filter = PartitionKey eq 'försäljnings- och efternamn eq ”Smith”  
* En ***tabells ökning*** inkluderar inte **PartitionKey** och är väldigt ineffektiv eftersom den söker igenom alla partitioner som utgör tabellen i tur och följd för alla matchande entiteter. Den utför en tabellgenomsökning oavsett om ditt filter använder den **RowKey**. Till exempel: $filter = LastName eq ”Jones”  
* Frågor som returnerar flera entiteter returnera dem sorterad i **PartitionKey** och **RowKey** ordning. För att undvika att behöva använda entiteter i klienten, Välj en **RowKey** som definierar de vanligaste sorteringsordning.  

Observera att om du använder en "**eller**" för att ange ett filter baserat på **RowKey** -värden resulterar det i en partitions ökning och behandlas inte som en områdes fråga. Därför bör du undvika frågor använder filter som: $filter = PartitionKey eq ”försäljning” och (RowKey eq '121' eller RowKey eq '322 ”)  

Exempel på klientsidan kod som använder Storage-klientbiblioteket för att köra effektiva frågor finns:  

* [Köra en punkt fråga med lagrings klient biblioteket](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Hämta flera entiteter med LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Projektion för serversidan](table-storage-design-patterns.md#server-side-projection)  

Exempel på klientsidan kod som kan hantera flera enhetstyper lagras i samma tabell finns:  

* [Arbeta med heterogena entitetstyper](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Välja en lämplig PartitionKey
Ditt val av **PartitionKey** ska stämma överens behovet av att aktivera användning av EGTs (för att säkerställa konsekvens) mot kravet att distribuera dina entiteter över flera partitioner (för att säkerställa en skalbar lösning).  

Vid en extreme du kan lagra alla entiteter i en enda partition, men detta kan begränsa skalbarheten i din lösning och kan förhindra tabelltjänsten från att kunna belastningsutjämna begäranden. På de andra Extreme kan du lagra en entitet per partition, som skulle vara mycket skalbar och som gör det möjligt för tabell tjänsten att belastningsutjämna begär Anden, men som hindrar dig från att använda enhets grupp transaktioner.  

Perfekt **PartitionKey** är en som gör att du kan använda effektiva frågor och som har tillräckligt med partitioner för att se till att din lösning är skalbar. Du hittar normalt att dina entiteter har en lämplig egenskap som distribuerar dina entiteter i tillräckligt med partitioner.

> [!NOTE]
> I ett system som lagrar information om användare eller anställda, till exempel vara användar-ID en bra PartitionKey. Du kan ha flera entiteter som använder ett visst användar-ID som partitionsnyckel. Varje entitet som lagrar data om en användare är grupperade i en enda partition och så dessa entiteter är tillgängliga via entitetsgrupptransaktioner, samtidigt som det är mycket skalbara.
> 
> 

Det finns ytterligare saker du behöver tänka på när du väljer **PartitionKey** som relaterar till hur du ska infoga, uppdatera och ta bort entiteter. Mer information finns i [utforma tabeller för data ändring](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>Optimera frågor för Table service
Table service indexerar automatiskt dina entiteter med den **PartitionKey** och **RowKey** värden i ett enda grupperat index, därför orsaken till att peka frågor är det mest effektiva sättet att använda. Det finns dock några index än som på det grupperade indexet på den **PartitionKey** och **RowKey**.

Många design måste uppfylla kraven för att aktivera sökning efter enheter baserat på flera villkor. Hitta exempelvis anställdas enheter baserat på e-post, anställnings-id eller efternamn. Mönstren som beskrivs i [tabell design mönster](table-storage-design-patterns.md) behandlar de här typerna av krav och beskriver olika sätt att arbeta runt det faktum att Table service inte tillhandahåller sekundära index:  

* [Intra-partition sekundärt index mönstret](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -Store flera kopior av varje entitet med hjälp av olika **RowKey** värden (i samma partition) för att aktivera snabb och effektiv uppslag och alternativ sorteringsordningar med hjälp av olika **RowKey** värden.  
* [Mönster för kommunikation mellan sekundära Partitionsindex](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) -Store flera kopior av varje entitet med hjälp av olika **RowKey** värden i separata partitioner eller i separata tabeller för att aktivera snabb och effektiv uppslag och alternativ sortera order med hjälp av olika **RowKey** värden.  
* [Index entiteter mönstret](table-storage-design-patterns.md#index-entities-pattern) -Underhåll index entiteter för att aktivera effektiv sökning som returnerar en lista över entiteter.  

## <a name="sorting-data-in-the-table-service"></a>Sortera data i Table service
Table service returnerar entiteter sorterade i stigande ordning baserat på **PartitionKey** och sedan efter **RowKey**. De här nycklarna är strängvärden och för att säkerställa att numeriska värden sorteras korrekt, bör du konvertera dem till en fast längd och fylla dem med nollor. Exempelvis anställdas id-värde som du använder som den **RowKey** är ett heltal, bör du konvertera anställnings-id **123** till **00000123**.  

Många program har kraven för att använda data som sorterats i olika ordning: till exempel sortera anställda efter namn eller genom att gå med datum. Följande mönster hanterar hur du kan använda alternativa sorterings ordningar för dina entiteter:  

* [Intra-partition sekundärt index mönstret](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – Store flera kopior av varje entitet som använder olika RowKey värden (i samma partition) för att aktivera snabb och effektiv uppslag och alternativ sortera beställningar med hjälp av olika RowKey värden.  
* [Mönster för kommunikation mellan sekundära Partitionsindex](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – Store flera kopior av varje entitet med hjälp av olika RowKey värden i separata partitioner i separata tabeller vill aktivera snabb och effektiv uppslag och alternativ sortera beställningar med hjälp av olika RowKey värden .
* [Log tail mönstret](table-storage-design-patterns.md#log-tail-pattern) -hämta den *n* entiteter som nyligen lagt till en partition med hjälp av en **RowKey** värde som sorteras listan i omvänd datum- och tidsordning.  

## <a name="next-steps"></a>Nästa steg

- [Tabell design mönster](table-storage-design-patterns.md)
- [Modellerings relationer](table-storage-design-modeling.md)
- [Kryptera tabell data](table-storage-design-encrypt-data.md)
- [Design för data ändring](table-storage-design-for-modification.md)

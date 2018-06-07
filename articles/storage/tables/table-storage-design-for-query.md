---
title: Utforma Azure storage-tabeller för frågor | Microsoft Docs
description: Skapa tabeller för frågor i Azure table storage.
services: storage
documentationcenter: na
author: MarkMcGeeAtAquent
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: b8d2033b0b29caddf165f4b582c7d0578109480c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34661073"
---
# <a name="design-for-querying"></a>Design för frågor
Tabell tjänstelösningar får läsas intensiva, Skriv intensiva eller en blandning av båda. Den här artikeln fokuserar på saker att ha i åtanke när du utformar din tabell-tjänsten för att stödja läsåtgärder effektivt. Vanligtvis är en design som stöder läsåtgärder effektivt också effektivt för skrivåtgärder. Men det finns ytterligare överväganden att ha i åtanke när du designar för att stödja skrivåtgärder, beskrivs i artikeln [Design för dataändring](table-storage-design-for-modification.md).

En bra utgångspunkt för att utforma din lösning för tabell så att du kan läsa data på ett effektivt sätt är att be ”vilka frågor mitt program måste köras för att hämta data som behövs från tabelltjänsten”?  

> [!NOTE]
> Det är viktigt att hämta designen rätt direkt eftersom det är svåra och dyra att ändra dem senare med tabell-tjänsten. Till exempel i en relationsdatabas är det ofta går att åtgärda problem med prestanda genom att lägga till index i en befintlig databas: Detta är inte ett alternativ med tabelltjänsten.  
> 
> 

Det här avsnittet fokuserar på viktiga problem som du måste ta när du designar tabeller för frågor. I det här avsnittet behandlar:

* [Hur du väljer PartitionKey och RowKey påverkar prestanda för frågor](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Att välja en lämplig PartitionKey](#choosing-an-appropriate-partitionkey)
* [Optimera frågor för tabelltjänsten](#optimizing-queries-for-the-table-service)
* [Sortera data i tabelltjänsten](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Hur du väljer PartitionKey och RowKey påverkar prestanda för frågor
Följande exempel förutsätter tabelltjänsten lagrar medarbetare entiteter med följande struktur (de flesta av exempel utelämna den **tidsstämpel** egenskapen för tydlighetens skull):  

| *Kolumnnamn* | *Datatyp* |
| --- | --- |
| **PartitionKey** (avdelningsnamn) |Sträng |
| **RowKey** (anställnings-Id) |Sträng |
| **Förnamn** |Sträng |
| **Efternamn** |Sträng |
| **ålder** |Integer |
| **E-postadress** |Sträng |

Artikeln [översikt över Azure Table storage](table-storage-overview.md) beskrivs några av de viktigaste funktionerna i Azure Table-tjänsten som har en direkt inverkan på utformning för frågan. Dessa resultera i följande allmänna riktlinjer för att utforma tabellen service frågor. Observera att filtersyntaxen som används i exemplen nedan är från tabelltjänsten REST API för mer information finns i [fråga entiteter](https://docs.microsoft.com/rest/api/storageservices/Query-Entities).  

* En ***punkt frågan*** är den mest effektiva sökningen att använda och rekommenderas som ska användas för stora volymer sökningar eller sökningar som kräver lägsta svarstid. Sådan fråga kan använda index för att hitta en enskild entitet mycket effektivt genom att ange både den **PartitionKey** och **RowKey** värden. Till exempel: $filter = (PartitionKey eq 'Sales') och (RowKey eq '2')  
* Andra bäst är en ***intervallet frågan*** som använder den **PartitionKey** och filter på en mängd **RowKey** värden för att returnera mer än en entitet. Den **PartitionKey** värdet identifierar en specifik partition och **RowKey** identifierar en delmängd av entiteter i partitionen. Till exempel: $filter = PartitionKey eq 'Försäljning och RowKey ge' och RowKey lt t '  
* Tredje bästa är en ***Partition skanna*** som använder den **PartitionKey** och filter på en annan icke-nyckelegenskapen och som kan returnera flera enheter. Den **PartitionKey** värdet identifierar en specifik partition och egenskapen värden väljer för en delmängd av entiteter i partitionen. Till exempel: $filter = PartitionKey eq 'Försäljning' och efternamn eq ”Smith”  
* En ***tabell skanna*** innehåller inte den **PartitionKey** och är mycket ineffektiv eftersom den söker igenom alla partitioner som utgör din tabell i sin tur för alla matchande entiteter. Utför en tabellgenomsökning oavsett om huruvida filtret använder den **RowKey**. Till exempel: $filter = efternamn eq 'Karlsson'  
* Frågor som returnerar flera entiteter tillbaka dem i **PartitionKey** och **RowKey** ordning. För att undvika sortera entiteter i klienten kan välja en **RowKey** som definierar de vanligaste sorteringsordning.  

Observera att använda en ”**eller**” att ange ett filter baserat på **RowKey** värden resulterar i en partition genomsökning och inte behandlas som en fråga för intervallet. Därför bör du undvika frågor som använder filter exempelvis: $filter = PartitionKey eq 'Sales' och (RowKey eq '121' eller RowKey eq '322')  

Exempel på klientsidan kod som använder Storage-klientbiblioteket för att köra effektiva frågor finns:  

* [Köra en punkt-fråga med Storage-klientbibliotek](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Hämta flera entiteter med hjälp av LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Projektion av serversidan](table-storage-design-patterns.md#server-side-projection)  

För exempel på klientsidan kod som kan hantera flera enhetstyper lagras i samma tabell, se:  

* [Arbeta med heterogena entitetstyper](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Att välja en lämplig PartitionKey
Ditt val av **PartitionKey** behovet av att aktivera användning av EGTs (för att säkerställa konsekvens) ska utjämna mot kravet att distribuera din entiteter över flera partitioner (för att säkerställa en skalbar lösning).  

Vid en extreme kan du lagra alla entiteter i en enda partition, men detta kan begränsa skalbarhet i din lösning och tabelltjänsten skulle förhindra att kunna belastningsutjämna förfrågningar. På det andra extremt kan du lagra en entitet per partition, vilket är mycket skalbart och som gör att tabelltjänsten för att belastningsutjämna förfrågningar, men som skulle kunna hindra att du använder enheten grupptransaktioner.  

Perfekt **PartitionKey** är en som gör att du kan använda effektiva frågor och som har tillräcklig partitioner för att se till att din lösning är skalbart. Du hittar normalt att-enheterna kommer att ha en lämplig egenskap som distribuerar entiteter i tillräcklig partitioner.

> [!NOTE]
> I ett system som lagrar information om användare eller anställda kanske användar-ID för en bra PartitionKey. Du kan ha flera enheter som använder en viss användar-ID som partitionsnyckel. Varje entitet som lagrar data om en användare är grupperade i en enda partition och så dessa enheter är tillgängliga via entitet grupptransaktioner, samtidigt som de skalbara.
> 
> 

Det finns fler överväganden i valfri **PartitionKey** som relaterar till hur du ska infoga, uppdatera och ta bort enheter. Mer information finns i [skapar tabeller för dataändring](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>Optimera frågor för tabelltjänsten
Tabelltjänsten indexerar automatiskt dina enheter med hjälp av den **PartitionKey** och **RowKey** värden i ett enda grupperat index, därför orsaken till att peka frågor är det mest effektiva sättet att använda. Det finns dock ingen index än det som i det grupperade indexet på den **PartitionKey** och **RowKey**.

Många-Designer måste uppfylla kraven för att aktivera sökning efter enheter baserat på flera villkor. Hitta medarbetare enheter baserat på e-post, till exempel anställnings-id eller efternamn. Mönster som beskrivs i [tabell designmönster](table-storage-design-patterns.md) dessa typer av krav och beskriver fungerar runt det faktum att tabelltjänsten inte ger sekundärindex på olika sätt:  

* [Intra-partition sekundärt index mönster](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -lagra flera kopior av varje enhet med hjälp av olika **RowKey** värden (i samma partition) för att aktivera snabb och effektiv sökningar och alternativa sorteringsordningen genom att använda olika **RowKey** värden.  
* [Mellan sekundära Partitionsindex mönster](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) -lagra flera kopior av varje enhet med hjälp av olika **RowKey** värden i separata partitioner eller i separata tabeller för att aktivera snabb och effektiv sökningar och alternativa sorteringen order med hjälp av olika **RowKey** värden.  
* [Index entiteter mönster](table-storage-design-patterns.md#index-entities-pattern) -Underhåll index enheter om du vill aktivera effektiva sökningar som returnerar en lista över enheter.  

## <a name="sorting-data-in-the-table-service"></a>Sortera data i tabelltjänsten
Tabelltjänsten returnerar entiteter sorterade i stigande ordning utifrån **PartitionKey** och sedan efter **RowKey**. Nycklarna är strängvärden och för att säkerställa att numeriska värden sorteras korrekt, bör du konvertera dem till en fast längd och Fyll ut dem med nollor. Om medarbetaren ID-värde som du använder som till exempel den **RowKey** är ett heltal, bör du konvertera anställnings-id **123** till **00000123**.  

Många program har krav för att använda data sorteras i olika ordning: till exempel sortera anställda efter namn eller ansluta till datum. Följande mönster handlar om hur du alternativ sorteringsordningar för dina enheter:  

* [Intra-partition sekundärt index mönster](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – lagra flera kopior av varje entitet som använder olika RowKey värden (i samma partition) för att aktivera snabb och effektiv sökningar och alternativa sorteringen sorterar med hjälp av olika RowKey värden.  
* [Mellan sekundära Partitionsindex mönster](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – lagra flera kopior av varje entitet med olika RowKey värden för olika partitioner i separata tabeller för att aktivera snabb och effektiv sökningar och alternativa sorteringen sorterar med hjälp av olika RowKey värden .
* [Loggen pilslut mönster](table-storage-design-patterns.md#log-tail-pattern) -hämta den *n* entiteter som senast lades till en partition med hjälp av en **RowKey** värde som sorterar i omvänd datum och tid ordning.  

## <a name="next-steps"></a>Nästa steg

- [Designmönster för tabellen](table-storage-design-patterns.md)
- [Modeling relationer](table-storage-design-modeling.md)
- [Kryptera tabelldata](table-storage-design-encrypt-data.md)
- [Design för dataändring](table-storage-design-for-modification.md)

---
title: Designguide för Azure Storage-tabellen | Microsoft Docs
description: Design skalbar och Performant tabeller i Azure-tabellagring
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: na
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: sngun
ms.openlocfilehash: 4f3cafd80c713697a8b8fdde56c021be1c5319fb
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824595"
---
# <a name="azure-storage-table-design-guide-designing-scalable-and-performant-tables"></a>Designguide för Azure Storage-tabellen: Utforma skalbar och Performant tabeller
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Design skalbar och performant tabeller måste du tänka på ett antal faktorer, till exempel prestanda, skalbarhet och kostnader. Om du har tidigare designat scheman för relationsdatabaser, dessa överväganden känna till dig, men det finns vissa likheter mellan Azure Table storage modell och relationella modeller, men det finns också många viktiga skillnader. Dessa skillnader vanligtvis leda till väldigt annorlunda design som kan se krånglig eller felaktig till någon bekant med relationsdatabaser, men som gör bra uppfattning om du designar för en NoSQL nyckel/värde-arkivet, t.ex Azure Table-tjänsten. Många av din design skillnader motsvarar det faktum att tabelltjänsten är utformad för att stödja skalbar molnlagring program som kan innehålla miljarder entiteter (rader i en relationsdatabas terminologi) av data eller för datauppsättningar som måste ha stöd för hög transaktionsvolymer: därför måste du tänka på olika sätt på hur du lagrar data och förstå hur tabelltjänsten fungerar. En väl utformad NoSQL-databas kan aktivera din lösning för att skala mycket mer (och till en lägre kostnad) än en lösning som använder en relationsdatabas. Den här guiden hjälper dig med dessa frågor.  

## <a name="about-the-azure-table-service"></a>Om tjänsten Azure Table
Det här avsnittet beskrivs några av de viktigaste funktionerna i tabelltjänsten som är särskilt relevanta till utformning för prestanda och skalbarhet. Om du har använt Azure Storage- och tabelltjänsten, läser du först [introduktion till Microsoft Azure Storage](../storage/common/storage-introduction.md) och [Kom igång med Azure Table Storage med hjälp av .NET](table-storage-how-to-use-dotnet.md) innan du läser resten av den här artikeln . Även om den här guiden fokuserar på tabelltjänsten, innehåller en beskrivning av Azure Queue och Blob-tjänster och hur du kan använda dem tillsammans med tjänsten tabellen i en lösning.  

Vad är tabelltjänsten? Som du kan förvänta sig från namnet använder tabelltjänsten tabellformat för att lagra data. Varje rad i tabellen representerar en entitet i standard terminologi och kolumnerna lagra olika egenskaper för enheten. Varje entitet har ett par nycklar för att identifiera den och en tidsstämpelkolumn som tabelltjänsten använder för att spåra när entiteten senast uppdaterade (detta sker automatiskt och du kan inte skriva över tidsstämpeln manuellt med ett godtyckligt värde). Tabelltjänsten använder den här last-modified-tidsstämpel (LMT) för att hantera Optimistisk samtidighet.  

> [!NOTE]
> REST API för tabellen tjänståtgärderna också returnera en **ETag** värde som den härleds från last-modified-tidsstämpeln (LMT). I det här dokumentet ska vi använda villkoren ETag och LMT synonymt eftersom de refererar till samma underliggande data.  
> 
> 

I följande exempel visas ett enkelt tabelldesign att lagra medarbetare och avdelning entiteter. Många av exemplen senare i den här handboken är baserade på den här enkla designen.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Tidsstämpel</th>
<th></th>
</tr>
<tr>
<td>Marknadsföring</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>Förnamn</th>
<th>Efternamn</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Ta</td>
<td>Hall</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marknadsföring</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>Förnamn</th>
<th>Efternamn</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Jun</td>
<td>CaO</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marknadsföring</td>
<td>Avdelning</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>Avdelningsnamn</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marknadsföring</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Försäljning</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>Förnamn</th>
<th>Efternamn</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Hittills har liknar detta en tabell i en relationsdatabas med de viktigaste skillnaderna är de obligatoriska kolumnerna och möjlighet att lagra flera typer av enheter i samma tabell. Dessutom var och en av de användardefinierade egenskaperna som **Förnamn** eller **ålder** har en datatyp som heltal eller en sträng, precis som en kolumn i en relationsdatabas. Men till skillnad från i en relationsdatabas tabelltjänsten schemat mindre uppbyggnad innebär att en egenskap måste ha samma datatyp för varje entitet. Om du vill lagra komplexa datatyper i en enskild egenskap, måste du använda ett serialiserat format, till exempel JSON eller XML. Mer information om datatyper i tabellen service som stöds, stöds datumintervall, namngivningsregler och storlek restriktioner finns [förstå den tabelltjänst-datamodellen](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Eftersom du kommer att se ditt val av **PartitionKey** och **RowKey** är grundläggande för bra tabelldesign. Varje entitet som lagras i en tabell måste ha en unik kombination av **PartitionKey** och **RowKey**. Precis som med nycklar i en relationsdatabas tabell den **PartitionKey** och **RowKey** värden indexeras om du vill skapa ett grupperat index som möjliggör snabb look-ups; men tabelltjänsten skapar inte någon sekundärindex så att de bara två indexerade egenskaper (vissa av de mönster som beskrivs senare visar hur du kan undvika den här tydligt begränsningen).  

En tabell består av en eller flera partitioner och som du kommer att se många designbeslut som du kommer att runt att välja en lämplig **PartitionKey** och **RowKey** att optimera din lösning. En lösning kan bestå av bara en enda tabell som innehåller alla entiteter indelade i partitioner, men vanligtvis en lösning har flera tabeller. Tabeller hjälper dig att ordna dina enheter, hjälper dig att hantera åtkomst till data med åtkomstkontrollistor logiskt och du kan släppa en hel tabell med hjälp av en enda lagring-åtgärd.  

### <a name="table-partitions"></a>Tabellpartitioner
Kontonamn, tabellnamnet och **PartitionKey** identifierar tillsammans partitionen i lagringstjänsten där tabelltjänsten lagrar entiteten. Samt som en del av adresseringsschema för entiteter partitioner för att definiera ett omfång för transaktioner (se [entitet gruppera transaktioner](#entity-group-transactions) nedan), och utgör grunden för hur tabelltjänsten skalas. Läs mer på partitioner [Azure Storage skalbarhets- och prestandamål](../storage/common/storage-scalability-targets.md).  

I tabellen service är en enskild nod tjänsterna eller mer Slutför partitioner och tjänsten skalar av dynamiskt nätverksbelastning partitioner mellan noder. Om en nod under belastning kan tabelltjänsten *dela* antal partitioner som underhålls av noden på olika noder; när trafik subsides tjänsten kan *merge* partition intervall från tyst noder tillbaka till en enda nod.  

Mer information om internt information för tabelltjänsten, särskilt hur tjänsten hanterar partitioner, finns i dokumentet [Microsoft Azure Storage: A hög Available Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Entiteten gruppera transaktioner
I tjänsten tabell är entitet grupptransaktioner (EGTs) den inbyggda mekanismen för att utföra atomiska uppdateringar över flera enheter. EGTs också kallas *batch transaktioner* i viss dokumentation. EGTs fungerar bara med entiteter som lagras i samma partition (dela samma partitionsnyckel i en given tabell), så vilja ha atomiska transaktionella beteende över flera enheter måste du se till att dessa enheter finns i samma partition. Det här är ofta en orsak till att ha flera typer av enheter i samma tabell (och partition) och inte använder flera tabeller för olika enhetstyper. En enda EGT kan tillämpas på högst 100 entiteter.  Om du skickar flera samtidiga EGTs för bearbetning är det viktigt att se till att dessa EGTs inte fungerar på enheter som är gemensamma mellan EGTs som annars bearbetning kan vara fördröjd.

EGTs också introducera en potentiell kompromiss där du kan utvärdera i din design: använda fler partitioner ökar skalbarheten för ditt program eftersom Azure har flera möjligheter för belastningsutjämning begäranden mellan noder, men detta kan begränsa möjligheten för ditt program att utföra atomiska transaktioner och underhålla stark konsekvens för dina data. Dessutom finns specifika skalbarhetsmål på nivån för en partition som kan begränsa genomflödet av transaktioner som du kan förvänta dig för en enskild nod: Läs mer om skalbarhetsmål för Azure storage-konton och tabelltjänsten [Azure Storage skalbarhets- och prestandamål](../storage/common/storage-scalability-targets.md). Senare avsnitt i den här handboken beskrivs olika design strategier som kan hjälpa dig att hantera avvägningarna som den här och diskutera hur du bäst för att välja din partitionsnyckel baserat på de särskilda kraven i ditt klientprogram.  

### <a name="capacity-considerations"></a>Överväganden för kapacitetsplanering
Följande tabell innehåller några nyckelvärden för att vara medveten om när du designar en lösning för tabell:  

| Total kapacitet för ett Azure storage-konto | 500 TB |
| --- | --- |
| Antalet tabeller i ett Azure storage-konto |Begränsas bara av kapaciteten för lagringskontot |
| Antalet partitioner i en tabell |Begränsas bara av kapaciteten för lagringskontot |
| Antal entiteter i en partition |Begränsas bara av kapaciteten för lagringskontot |
| Storleken på en enskild entitet |Upp till 1 MB med högst 255 egenskaper (inklusive den **PartitionKey**, **RowKey**, och **tidsstämpel**) |
| Storleken på den **PartitionKey** |En sträng i storlek på upp till 1 KB |
| Storleken på den **RowKey** |En sträng i storlek på upp till 1 KB |
| Storleken på en entitet grupp-transaktion |En transaktion får innehålla högst 100 entiteter och nyttolasten måste vara mindre än 4 MB i storlek. En entitet kan bara uppdatera en gång i en EGT. |

Mer information finns i [förstå den tabelltjänst-datamodellen](http://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Kostnad överväganden
Table storage är relativt billig, men du bör innehålla kostnaderna för både kapacitetsförbrukning och antalet transaktioner som en del av din utvärderingsversion av någon lösning som använder tabelltjänsten. I många scenarier för datalagring Avnormaliserade eller dubbletter för att förbättra är prestanda och skalbarhet för din lösning dock en giltig metod för att vidta. Mer information om priser finns [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Riktlinjer för tabelldesign
De här listorna sammanfattas några av de viktiga riktlinjer som du bör tänka på när du utformar dina tabeller och den här guiden att behandla dem i detalj senare i. Dessa riktlinjer är bland annat de riktlinjer som du vanligtvis utför för relationsdatabas design.  

Utforma din lösning för tabellen ska *läsa* effektivt:

* ***Design för frågor i Läs-aktiverat program.*** När du utformar dina tabeller tänka frågor (särskilt svarstid känsliga de) som du ska köra innan du tycker om hur du ska uppdatera-enheterna. Detta ger vanligtvis en effektiv och performant lösning.  
* ***Ange både PartitionKey och RowKey i dina frågor.*** *Peka frågor* som dessa är de mest effektiva tabellen service frågorna.  
* ***Överväg att lagra dubbletter av enheter.*** Table storage är billiga så du överväga att spara samma entitet flera gånger (med olika nycklar) om du vill aktivera effektivare frågor.  
* ***Överväg att denormalizing dina data.*** Table storage är billiga så Överväg denormalizing dina data. Lagra exempelvis sammanfattning enheter så att frågor för att samla in data bara behöver åtkomst till en enda entitet.  
* ***Använd sammansatt nyckelvärden.*** Är bara nycklar som du har **PartitionKey** och **RowKey**. Till exempel använda sammansatta nyckelvärden för att aktivera alternativ nycklad åtkomst sökvägar till enheter.  
* ***Använd fråga projektion.*** Du kan minska mängden data som du överför via nätverket med hjälp av frågor som väljer de fält du behöver.  

Utforma din lösning för tabellen ska *skriva* effektivt:  

* ***Skapa inte varm partitioner.*** Välj nycklar att sprida dina begäranden över flera partitioner vid någon tidpunkt.  
* ***Undvika toppar i trafiken.*** Utjämna trafiken över en rimlig tid och undvika toppar i trafiken.
* ***Inte nödvändigtvis att skapa en separat tabell för varje typ av enhet.*** När du behöver atomiska transaktioner över typer av enheter kan lagra du dessa flera typer av enheter i samma partition i samma tabell.
* ***Överväg att maximalt dataflöde du måste uppnå.*** Du måste vara medveten om skalbarhetsmål för tabelltjänsten och se till att designen inte ska orsaka du överskrida dem.  

När du läser handboken visas exempel placera alla dessa principer i praktiken.  

## <a name="design-for-querying"></a>Design för frågor
Tabell tjänstelösningar får läsas intensiva, Skriv intensiva eller en blandning av båda. Det här avsnittet handlar om saker att ha i åtanke när du utformar din tabell-tjänsten för att stödja läsåtgärder effektivt. Vanligtvis är en design som stöder läsåtgärder effektivt också effektivt för skrivåtgärder. Men det finns ytterligare överväganden att ha i åtanke när du skapar för att stödja skrivåtgärder som beskrivs i nästa avsnitt, [Design för dataändring](#design-for-data-modification).

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

### <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Hur du väljer PartitionKey och RowKey påverkar prestanda för frågor
Följande exempel förutsätter tabelltjänsten lagrar medarbetare entiteter med följande struktur (de flesta av exempel utelämna den **tidsstämpel** egenskapen för tydlighetens skull):  

| *Kolumnnamn* | *Datatyp* |
| --- | --- |
| **PartitionKey** (avdelningsnamn) |Sträng |
| **RowKey** (anställnings-Id) |Sträng |
| **Förnamn** |Sträng |
| **Efternamn** |Sträng |
| **ålder** |Integer |
| **E-postadress** |Sträng |

Avsnittet tidigare [översikt över Azure Table](#overview) beskrivs några av de viktigaste funktionerna i Azure Table-tjänsten som har en direkt inverkan på utformning för frågan. Dessa resultera i följande allmänna riktlinjer för att utforma tabellen service frågor. Observera att filtersyntaxen som används i exemplen nedan är från tabelltjänsten REST API för mer information finns i [fråga entiteter](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

* En ***punkt frågan*** är den mest effektiva sökningen att använda och rekommenderas som ska användas för stora volymer sökningar eller sökningar som kräver lägsta svarstid. Sådan fråga kan använda index för att hitta en enskild entitet mycket effektivt genom att ange både den **PartitionKey** och **RowKey** värden. Till exempel: $filter = (PartitionKey eq 'Sales') och (RowKey eq '2')  
* Andra bäst är en ***intervallet frågan*** som använder den **PartitionKey** och filter på en mängd **RowKey** värden för att returnera mer än en entitet. Den **PartitionKey** värdet identifierar en specifik partition och **RowKey** identifierar en delmängd av entiteter i partitionen. Till exempel: $filter = PartitionKey eq 'Försäljning och RowKey ge' och RowKey lt t '  
* Tredje bästa är en ***Partition skanna*** som använder den **PartitionKey** och filter på en annan icke-nyckelegenskapen och som kan returnera flera enheter. Den **PartitionKey** värdet identifierar en specifik partition och egenskapen värden väljer för en delmängd av entiteter i partitionen. Till exempel: $filter = PartitionKey eq 'Försäljning' och efternamn eq ”Smith”  
* En ***tabell skanna*** innehåller inte den **PartitionKey** och är mycket ineffektiv eftersom den söker igenom alla partitioner som utgör din tabell i sin tur för alla matchande entiteter. Utför en tabellgenomsökning oavsett om huruvida filtret använder den **RowKey**. Till exempel: $filter = efternamn eq 'Karlsson'  
* Frågor som returnerar flera entiteter tillbaka dem i **PartitionKey** och **RowKey** ordning. För att undvika sortera entiteter i klienten kan välja en **RowKey** som definierar de vanligaste sorteringsordning.  

Observera att använda en ”**eller**” att ange ett filter baserat på **RowKey** värden resulterar i en partition genomsökning och inte behandlas som en fråga för intervallet. Därför bör du undvika frågor som använder filter exempelvis: $filter = PartitionKey eq 'Sales' och (RowKey eq '121' eller RowKey eq '322')  

Exempel på klientsidan kod som använder Storage-klientbiblioteket för att köra effektiva frågor finns:  

* [Köra en punkt-fråga med Storage-klientbibliotek](#executing-a-point-query-using-the-storage-client-library)
* [Hämtning av flera enheter med hjälp av LINQ](#retrieving-multiple-entities-using-linq)
* [Projektion av serversidan](#server-side-projection)  

För exempel på klientsidan kod som kan hantera flera enhetstyper lagras i samma tabell, se:  

* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  

### <a name="choosing-an-appropriate-partitionkey"></a>Att välja en lämplig PartitionKey
Ditt val av **PartitionKey** behovet av att aktivera användning av EGTs (för att säkerställa konsekvens) ska utjämna mot kravet att distribuera din entiteter över flera partitioner (för att säkerställa en skalbar lösning).  

Vid en extreme kan du lagra alla entiteter i en enda partition, men detta kan begränsa skalbarhet i din lösning och tabelltjänsten skulle förhindra att kunna belastningsutjämna förfrågningar. På det andra extremt kan du lagra en entitet per partition, vilket är mycket skalbart och som gör att tabelltjänsten för att belastningsutjämna förfrågningar, men som skulle kunna hindra att du använder enheten grupptransaktioner.  

Perfekt **PartitionKey** är en som gör att du kan använda effektiva frågor och som har tillräcklig partitioner för att se till att din lösning är skalbart. Du hittar normalt att-enheterna kommer att ha en lämplig egenskap som distribuerar entiteter i tillräcklig partitioner.

> [!NOTE]
> I ett system som lagrar information om användare eller anställda kanske användar-ID för en bra PartitionKey. Du kan ha flera enheter som använder en viss användar-ID som partitionsnyckel. Varje entitet som lagrar data om en användare är grupperade i en enda partition och så dessa enheter är tillgängliga via entitet grupptransaktioner, samtidigt som de skalbara.
> 
> 

Det finns fler överväganden i valfri **PartitionKey** som relaterar till hur du ska infoga, uppdatera och ta bort entiteter: finns i avsnittet [Design för dataändring](#design-for-data-modification) nedan.  

### <a name="optimizing-queries-for-the-table-service"></a>Optimera frågor för tabelltjänsten
Tabelltjänsten indexerar automatiskt dina enheter med hjälp av den **PartitionKey** och **RowKey** värden i ett enda grupperat index, därför orsaken till att peka frågor är det mest effektiva sättet att använda. Det finns dock ingen index än det som i det grupperade indexet på den **PartitionKey** och **RowKey**.

Många-Designer måste uppfylla kraven för att aktivera sökning efter enheter baserat på flera villkor. Hitta medarbetare enheter baserat på e-post, till exempel anställnings-id eller efternamn. Följande mönster i avsnittet [tabell designmönster](#table-design-patterns) dessa typer av krav och beskriver fungerar runt det faktum att tabelltjänsten inte ger sekundärindex på olika sätt:  

* [Intra-partition sekundärt index mönster](#intra-partition-secondary-index-pattern) -lagra flera kopior av varje enhet med hjälp av olika **RowKey** värden (i samma partition) för att aktivera snabb och effektiv sökningar och alternativa sorteringsordningen genom att använda olika **RowKey** värden.  
* [Mellan sekundära Partitionsindex mönster](#inter-partition-secondary-index-pattern) -lagra flera kopior av varje enhet med hjälp av olika **RowKey** värden i separata partitioner eller i separata tabeller för att aktivera snabb och effektiv sökningar och alternativa sorteringen order med hjälp av olika **RowKey** värden.  
* [Index entiteter mönster](#index-entities-pattern) -Underhåll index enheter om du vill aktivera effektiva sökningar som returnerar en lista över enheter.  

### <a name="sorting-data-in-the-table-service"></a>Sortera data i tabelltjänsten
Tabelltjänsten returnerar entiteter sorterade i stigande ordning utifrån **PartitionKey** och sedan efter **RowKey**. Nycklarna är strängvärden och för att säkerställa att numeriska värden sorteras korrekt, bör du konvertera dem till en fast längd och Fyll ut dem med nollor. Om medarbetaren ID-värde som du använder som till exempel den **RowKey** är ett heltal, bör du konvertera anställnings-id **123** till **00000123**.  

Många program har krav för att använda data sorteras i olika ordning: till exempel sortera anställda efter namn eller ansluta till datum. Följande mönster i avsnittet [tabell designmönster](#table-design-patterns) adress så alternativa sorteringsordningar för dina enheter:  

* [Intra-partition sekundärt index mönster](#intra-partition-secondary-index-pattern) – lagra flera kopior av varje entitet som använder olika RowKey värden (i samma partition) för att aktivera snabb och effektiv sökningar och alternativa sorteringen sorterar med hjälp av olika RowKey värden.  
* [Mellan sekundära Partitionsindex mönster](#inter-partition-secondary-index-pattern) – lagra flera kopior av varje entitet med olika RowKey värden för olika partitioner i separata tabeller för att aktivera snabb och effektiv sökningar och alternativa sorteringen sorterar med hjälp av olika RowKey värden .
* [Loggen pilslut mönster](#log-tail-pattern) -hämta den *n* entiteter som senast lades till en partition med hjälp av en **RowKey** värde som sorterar i omvänd datum och tid ordning.  

## <a name="design-for-data-modification"></a>Design för dataändring
Det här avsnittet fokuserar på designöverväganden för att optimera infogningar, uppdateringar, och tar bort. I vissa fall behöver du utvärdera kompromissen mellan Designer optimerar för frågor mot Designer optimerar för dataändring precis som i Designer för relationsdatabaser (även om metoder för att hantera design avvägningarna är olika i en relationsdatabas). Avsnittet [tabell designmönster](#table-design-patterns) beskriver vissa detaljerad designmönster för tabelltjänsten och beskrivs några dessa avvägningarna. Du hittar många Designer som optimerats för att fråga entiteter också fungerar bra för att ändra entiteter i praktiken.  

### <a name="optimizing-the-performance-of-insert-update-and-delete-operations"></a>Optimera prestanda för insert-, update och delete-åtgärder
Om du vill uppdatera eller ta bort en entitet, du måste kunna identifieras med hjälp av den **PartitionKey** och **RowKey** värden. I detta avseende valet av **PartitionKey** och **RowKey** för ändra entiteter bör följa liknande kriterier till ditt val att stödja punkt frågor eftersom du vill identifiera enheter som effektiv som möjligt. Du inte vill använda en ineffektiv partition eller tabell sökning för att hitta en enhet för att identifiera den **PartitionKey** och **RowKey** värden som du behöver uppdatera eller ta bort den.  

Följande mönster i avsnittet [tabell designmönster](#table-design-patterns) adress optimera prestanda eller din insert, update och delete-åtgärder:  

* [Hög volym ta bort mönster](#high-volume-delete-pattern) -Aktivera borttagning av ett stort antal enheter genom att lagra alla entiteter för samtidiga borttagning i sina egna separata tabeller; ta bort entiteterna genom att ta bort tabellen.  
* [Serien datamönster](#data-series-pattern) -Store fullständig dataserier i en enda enhet för att minimera antalet begäranden som du gör.  
* [Wide entiteter mönster](#wide-entities-pattern) -använda flera fysiska enheter för att lagra logiska entiteter med mer än 252 egenskaper.  
* [Mönster för stora entiteter](#large-entities-pattern) -använda blob storage för att lagra stora egenskapsvärden.  

### <a name="ensuring-consistency-in-your-stored-entities"></a>Säkerställa konsekvens i lagrade entiteter
Den viktiga faktor som påverkar ditt val av nycklar för att optimera dataändringar är att säkerställa konsekvens med hjälp av atomiska transaktioner. Du kan bara använda en EGT ska fungera på entiteter som lagras i samma partition.  

Följande mönster i avsnittet [tabell designmönster](#table-design-patterns) adress hantera konsekvens:  

* [Intra-partition sekundärt index mönster](#intra-partition-secondary-index-pattern) -lagra flera kopior av varje enhet med hjälp av olika **RowKey** värden (i samma partition) för att aktivera snabb och effektiv sökningar och alternativa sorteringsordningen genom att använda olika **RowKey** värden.  
* [Mellan sekundära Partitionsindex mönster](#inter-partition-secondary-index-pattern) – lagra flera kopior av varje entitet som använder olika RowKey värden i olika partitioner eller i separata tabeller för att aktivera snabb och effektiv sökningar och alternativa sorteringen sorterar med hjälp av olika **RowKey** värden.  
* [Överensstämmelse transaktioner mönster](#eventually-consistent-transactions-pattern) -aktivera överensstämmelse beteende mellan partitionsgränser eller lagring system gränser med hjälp av Azure köer.
* [Index entiteter mönster](#index-entities-pattern) -Underhåll index enheter om du vill aktivera effektiva sökningar som returnerar en lista över enheter.  
* [Denormalization mönster](#denormalization-pattern) -kombinera relaterade data tillsammans i en enda enhet så att du kan hämta alla data som du behöver med en enda fråga.  
* [Serien datamönster](#data-series-pattern) -Store fullständig dataserier i en enda enhet för att minimera antalet begäranden som du gör.  

Information om entiteten grupptransaktioner finns i avsnittet [entitet gruppera transaktioner](#entity-group-transactions).  

### <a name="ensuring-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Säkerställa en design för effektiv ändringar underlättar effektiv frågor
I många fall bör alltid en design för effektiva frågor ger effektiv ändringar, men du utvärdera om så är fallet för din situation. Några av mönster i avsnittet [tabell designmönster](#table-design-patterns) explicit utvärdera avvägningarna mellan fråga och ändra entiteter och du bör alltid beakta numret för varje typ av åtgärd.  

Följande mönster i avsnittet [tabell designmönster](#table-design-patterns) adressen avvägningarna mellan utformning för effektiva frågor och designar för effektiv dataändring:  

* [Sammansatt nyckel mönster](#compound-key-pattern) -Använd sammansatta **RowKey** värden att aktivera en klient att söka efter relaterade data med en enda fråga.  
* [Loggen pilslut mönster](#log-tail-pattern) -hämta den *n* entiteter som senast lades till en partition med hjälp av en **RowKey** värde som sorterar i omvänd datum och tid ordning.  

## <a name="encrypting-table-data"></a>Kryptera tabelldata
.NET Azure Storage-klientbibliotek har stöd för kryptering av strängen Entitetsegenskaper för insert och ersätt-åtgärder. Krypterade strängar lagras på tjänsten som binära egenskaper och de konverteras till strängar efter dekrypteringen.    

För tabeller, förutom krypteringsprincipen, måste användare ange egenskaper som ska krypteras. Detta kan göras genom att antingen att ange attributet [EncryptProperty] (för POCO entiteter som är härledda från TableEntity) eller en kryptering matcharen begäran alternativ. En kryptering Konfliktlösaren är en delegat som tar en partitionsnyckel, radnyckel och egenskapsnamn och returnerar ett booleskt värde som anger om egenskapen ska krypteras. Under krypteringen använder klientbiblioteket den här informationen för att avgöra om en egenskap ska krypteras vid skrivning till kabeln. Delegaten ger också möjlighet att logik runt hur egenskaperna är krypterade. (Till exempel om X, sedan kryptera egenskapen A; annars kryptera egenskaper A och b) Observera att det inte nödvändigt att ange den här informationen vid läsning eller fråga entiteter.

Observera att koppling inte stöds för närvarande. Eftersom en delmängd av egenskaper kan har krypterats tidigare med hjälp av en annan nyckel leder bara sammanslagning nya egenskaper och uppdaterar metadata till dataförlust. Sammanslagning av antingen kräver extra service-anrop för att läsa den befintliga enheten från tjänsten eller med en ny nyckel för varje egenskap som lämpar sig inte av prestandaskäl.     

Information om hur du krypterar tabelldata finns [kryptering på klientsidan och Azure Key Vault för Microsoft Azure Storage](../storage/common/storage-client-side-encryption.md).  

## <a name="modelling-relationships"></a>Modellering relationer
Skapa domänmodeller är ett viktigt steg i utformningen av komplexa system. Normalt använder du modellering processen för att identifiera enheter och relationer mellan dem som ett sätt att förstå business-domänen och informera designen av systemet. Det här avsnittet fokuserar på hur du kan översätta några av de vanliga relation hittades i domänmodeller för Designer för tabelltjänsten. Processen för mappning från en logisk datamodell till en fysisk NoSQL baserad-datamodell är väldigt annorlunda än den som används när du skapar en relationsdatabas. Relationsdatabaser design förutsätter vanligtvis en process för normalisering som har optimerats för att minimera redundans – och en deklarativ frågar funktion som avlägsnar hur implementeringen av hur databasen fungerar.  

### <a name="one-to-many-relationships"></a>En-till-många-relationer
En-till-många-relationer mellan företag domänobjekt inträffa ofta: till exempel en avdelning har många anställda. Det finns flera sätt att implementera en-till-många-relationer i tabelltjänsten varje med för- och nackdelar som är relevanta för ett visst scenario.  

Studera exemplet i flera nationella stora företag med tusentals avdelningar och medarbetare enheter där varje avdelning har många anställda och varje medarbetare som tillhör en viss avdelning. En metod är att lagra separat avdelning och medarbetare entiteter som dessa:  

![][1]

Det här exemplet illustrerar en implicit en-till-många-relation mellan de typer som är baserat på den **PartitionKey** värde. Varje avdelning kan ha många anställda.  

Det här exemplet visar också en avdelning entiteten och dess relaterade medarbetare entiteter i samma partition. Du kan välja att använda olika partitioner, tabeller och även lagringskonton för olika enhetstyper.  

En annan metod är att denormalize dina data och lagrar bara medarbetare entiteter med Avnormaliserade avdelningsdata som visas i följande exempel. I det här scenariot kanske kan då Avnormaliserade inte bäst om du har ett krav för att kunna ändra information om en hanterare för avdelning eftersom om du vill göra detta måste du uppdatera alla medarbetare i avdelningen.  

![][2]

Mer information finns i [Denormalization mönster](#denormalization-pattern) senare i den här guiden.  

I följande tabell sammanfattas för- och nackdelarna med vart och ett av de metoder som beskrivs ovan för att lagra medarbetare och avdelningen enheter som har en en-till-många-relation. Det bör också övervägas hur ofta du förväntar dig att utföra olika åtgärder: kan det vara acceptabelt att ha en design som innehåller en kostsam åtgärd om åtgärden endast inträffar sällan.  

<table>
<tr>
<th>Metoden</th>
<th>Tekniker</th>
<th>Nackdelar</th>
</tr>
<tr>
<td>Separata entitetstyper, samma partition i samma tabell</td>
<td>
<ul>
<li>Du kan uppdatera en avdelning entitet med en enda åtgärd.</li>
<li>Du kan använda en EGT för att upprätthålla enhetliga om du har ett krav för att ändra en avdelning enhet när du uppdatering/insert/ta bort en medarbetare entitet. Till exempel om du har ett antal avdelningens anställda för varje avdelning.</li>
</ul>
</td>
<td>
<ul>
<li>Du kan behöva hämta både anställda och en avdelning entitet för vissa aktiviteter för klienten.</li>
<li>Storage-åtgärder sker i samma partition. Vid höga transaktionsvolymer kan detta resultera i en hotspot.</li>
<li>Du kan inte flytta en medarbetare till en ny avdelning med hjälp av en EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Separata entitetstyper, olika partitioner eller tabeller storage-konton</td>
<td>
<ul>
<li>Du kan uppdatera en avdelning entitet eller medarbetare enhet med en enda åtgärd.</li>
<li>Vid höga transaktionsvolymer att detta sprida belastningen över flera partitioner.</li>
</ul>
</td>
<td>
<ul>
<li>Du kan behöva hämta både anställda och en avdelning entitet för vissa aktiviteter för klienten.</li>
<li>Du kan inte använda EGTs för att underhålla konsekvensen när du uppdatering/insert/ta bort en medarbetare och uppdatera en avdelning. Till exempel uppdaterar ett antal anställda i en avdelning entitet.</li>
<li>Du kan inte flytta en medarbetare till en ny avdelning med hjälp av en EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalize till samma enhetstyp</td>
<td>
<ul>
<li>Du kan hämta all information du behöver med en enskild begäran.</li>
</ul>
</td>
<td>
<ul>
<li>Det kan vara dyra att upprätthålla enhetliga om du behöver uppdatera avdelning information (det här kräver du uppdatera alla anställda i en avdelning).</li>
</ul>
</td>
</tr>
</table>

Hur du väljer mellan dessa alternativ och vilka av för- och nackdelar som är störst, beror på dina specifika Programscenarier. Till exempel hur ofta du ändrar avdelning entiteter; behöver alla medarbetare frågor kan ytterligare information som avdelningsnivå; hur nära kommer du skalbarhetsgränser på partitioner eller storage-konto?  

### <a name="one-to-one-relationships"></a>1: 1-relationer
Domänmodeller kan innehålla 1: 1-relationer mellan entiteter. Om du behöver implementera en-till-en relation i tabelltjänsten måste du också välja hur du länkar två relaterade entiteter när du behöver hämta dem båda. Den här länken kan vara implicit, baserat på en konvention i nyckelvärdena eller explicit genom att lagra en länk i form av **PartitionKey** och **RowKey** värdena i varje entitet till dess relaterade entitet. Mer information om huruvida du bör lagra relaterade entiteter i samma partition, finns i avsnittet [en-till-många-relationer](#one-to-many-relationships).  

Observera att det finns också implementering som kan leda dig att implementera 1: 1-relationer i tabelltjänsten:  

* Hantering av stora entiteter (Mer information finns i [stora entiteter mönster](#large-entities-pattern)).  
* Implementera åtkomstkontroller (Mer information finns i [Kontrollera åtkomst med signaturer för delad åtkomst](#controlling-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Delta i klient
Även om det finns olika sätt att modellen relationer i tabelltjänsten, ska du inte glömma att två huvudsakliga skälen för att använda tabelltjänsten finns skalbarhet och prestanda. Om du hittar du modellering många relationer som kan påverka prestanda och skalbarhet i lösningen, bör du fråga dig själv om det är nödvändigt att skapa alla relationer som data i tabelldesign. Du kan förenkla designen och förbättra skalbarhet och prestanda i lösningen om du låta ditt klientprogram som utför alla nödvändiga kopplingar.  

Till exempel om du har liten tabeller som innehåller data som inte ändras ofta kan sedan du hämta dessa data en gång och cachelagras på klienten. Detta kan undvika upprepade görs för att hämta samma data. I exemplen som vi har tittat på i den här handboken sannolikt uppsättning avdelningar i en liten organisation att små och ändra sällan att göra det en bra kandidat för data som klientprogram kan ladda ned en gång och cache som slå upp data.  

### <a name="inheritance-relationships"></a>Arvsrelationer
Om klientprogrammet använder en uppsättning klasser som ingår i en arvsrelation för att representera affärsobjekt, kan du enkelt kvarstår dessa enheter i tabelltjänsten. Du kan till exempel ha följande uppsättning klasser som definieras i ditt klientprogram där **Person** är en abstrakt klass.

![][3]

Du kan spara instanser av två konkreta klasser i tjänsten tabellen med hjälp av en enskild Person tabell med entiteter i den ser ut så här:  

![][4]

Mer information om hur du arbetar med flera typer av enheter i samma tabell i klientkod finns i avsnittet [arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types) senare i den här guiden. Detta ger exempel på hur du identifierar entitetstypen i klientkod.  

## <a name="table-design-patterns"></a>Designmönster för tabellen
Du har sett detaljerad diskussioner om hur du optimerar dina tabelldesign för både hämtades entitetsdata med hjälp av frågor och infoga, uppdatera och ta bort entitetsdata i föregående avsnitt. Det här avsnittet beskrivs vissa mönster som är lämpliga för användning med tjänstelösningar för tabellen. Dessutom visas hur du praktiskt taget kan lösa vissa problem och avvägningarna aktiveras tidigare i den här guiden. I följande diagram visas relationerna mellan de olika mönster:  

![][5]

Mönstret kartan ovan visar relationer mellan mönster (blå) och ett mönster (orange) som finns dokumenterade i handboken. Det är naturligtvis många mönster som är värda att ta hänsyn till. Till exempel ett viktiga scenarier för Tabelltjänsten är att använda den [Materialiserade vyn mönster](https://msdn.microsoft.com/library/azure/dn589782.aspx) från den [kommandot frågan ansvar ansvarsfördelning (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) mönster.  

### <a name="intra-partition-secondary-index-pattern"></a>Intra-partition sekundärt index mönster
Lagra flera kopior av varje enhet med hjälp av olika **RowKey** värden (i samma partition) att aktivera snabb och effektiv sökningar och alternativa sorteringsordningen genom att använda olika **RowKey** värden. Uppdateringar mellan kopior kan vara konsekvent med EGT'S.  

#### <a name="context-and-problem"></a>Kontext och problem
Tabelltjänsten indexerar automatiskt enheter med hjälp av den **PartitionKey** och **RowKey** värden. Detta gör att ett klientprogram att hämta en entitet som effektivt använder dessa värden. Till exempel använder tabellstrukturen som visas nedan, ett klientprogram kan använda en punkt-fråga för att hämta en enskild medarbetare entitet med id och ett avdelningsnamn (den **PartitionKey** och **RowKey**  värden). En klient kan också hämta entiteter sorterade efter anställnings-id inom varje avdelning.

![][6]

Om du vill kunna hitta en medarbetare entitet baserat på värdet för en annan egenskap, t.ex e-postadress, måste du använda en mindre effektiv partition sökning för att hitta en matchande. Det beror på att tabelltjänsten inte ger sekundärindex. Dessutom kan det går inte att begära en lista över anställda sorterade i en annan ordning än **RowKey** ordning.  

#### <a name="solution"></a>Lösning
Undvik bristande sekundärindex, kan du lagra flera kopior av varje entitet med varje kopia med ett annat **RowKey** värde. Om du sparar en entitet med strukturer som visas nedan, kan du effektivt hämta medarbetare enheter baserat på e-postadress eller medarbetare id. Prefixet värden för den **RowKey**, ”empid_” och ”email_” kan du fråga efter en medarbetare eller ett intervall med anställda med hjälp av en mängd e-postadresser eller medarbetare-ID: n.  

![][7]

Följande två filtervillkoren (en slå upp av anställnings-id och en sökning efter av e-postadress) ange både punkt frågor:  

* $filter = (PartitionKey eq 'Sales') och (RowKey eq 'empid_000223')  
* $filter = (PartitionKey eq 'Sales') och (RowKey eq 'email_jonesj@contoso.com')  

Om du frågar efter ett intervall med anställdas enheter du kan ange ett intervall i medarbetare id ordning eller ett intervall som är sorterad i e-postadress ordning genom att fråga om entiteter med ett prefix i den **RowKey**.  

* Hitta alla anställda på försäljningsavdelningen med medarbetare id i intervallet 000100 000199 användning: $filter = (PartitionKey eq 'Sales') och (RowKey ge 'empid_000100') och (RowKey le 'empid_000199')  
* Hitta alla anställda på försäljningsavdelningen med en e-postadress som börjar med bokstaven ”a” Använd: $filter = (PartitionKey eq 'Sales') och (RowKey ge 'email_a') och (RowKey lt 'email_b')  
  
  Observera att filtersyntaxen som används i exemplen ovan är från tabelltjänsten REST API för mer information finns i [fråga entiteter](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Table storage är relativt billig använder så overhead kostnaden för lagring av duplicerade data inte får vara ett större problem. Du bör dock alltid utvärdera kostnaden för din design utifrån din förväntade lagringsbehov och bara lägga till dubbla entiteter för att stödja frågorna client-program körs.  
* Eftersom entiteterna sekundärt index lagras i samma partition som de ursprungliga enheterna, bör du kontrollera att du inte överskrider skalbarhetsmål för en enskild partition.  
* Du kan behålla-dubbla enheterna överensstämmer med varandra med hjälp av EGTs att automatiskt uppdatera två kopior av entiteten. Detta innebär att du bör lagra alla kopior av en entitet i samma partition. Mer information finns i avsnittet [med hjälp av entiteten gruppera transaktioner](#entity-group-transactions).  
* Det värde som används för den **RowKey** måste vara unikt för varje entitet. Överväg att använda sammansatta nyckelvärden.  
* Utfyllnad numeriska värden i den **RowKey** (exempelvis anställnings-id 000223), aktiverar korrigera sortera och filtrera baserat på övre och nedre gränser.  
* Du behöver inte nödvändigtvis att kopiera alla egenskaper för entiteten. Till exempel om frågorna som sökning entiteter med den e-posten-adressen i den **RowKey** behöver aldrig medarbetarens ålder, dessa enheter kan ha följande struktur:

![][8]

* Det är vanligtvis bättre att lagra duplicerade data och se till att du kan hämta alla data som du behöver med en enskild fråga än att använda en fråga för att hitta en enhet och en annan för att söka efter data som krävs.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när klientprogrammet måste hämta entiteter med olika nycklar olika när klienten behöver hämta entiteter i olika sorteringsordningar, och där du kan identifiera varje entitet med olika unika värden. Dock bör du se till att du inte överskrider skalbarhetsgränser partition när du utför entitet sökningar med hjälp av de olika **RowKey** värden.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Mellan sekundära Partitionsindex mönster](#inter-partition-secondary-index-pattern)
* [Sammansatt nyckel mönster](#compound-key-pattern)
* [Entiteten gruppera transaktioner](#entity-group-transactions)
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Mellan sekundära Partitionsindex mönster
Lagra flera kopior av varje enhet med hjälp av olika **RowKey** värden i separata partitioner eller i separata tabeller för att aktivera snabb och effektiv sökningar och alternativa sorteringsordningen genom att använda olika **RowKey**värden.  

#### <a name="context-and-problem"></a>Kontext och problem
Tabelltjänsten indexerar automatiskt enheter med hjälp av den **PartitionKey** och **RowKey** värden. Detta gör att ett klientprogram att hämta en entitet som effektivt använder dessa värden. Till exempel använder tabellstrukturen som visas nedan, ett klientprogram kan använda en punkt-fråga för att hämta en enskild medarbetare entitet med id och ett avdelningsnamn (den **PartitionKey** och **RowKey**  värden). En klient kan också hämta entiteter sorterade efter anställnings-id inom varje avdelning.  

![][9]

Om du vill kunna hitta en medarbetare entitet baserat på värdet för en annan egenskap, t.ex e-postadress, måste du använda en mindre effektiv partition sökning för att hitta en matchande. Det beror på att tabelltjänsten inte ger sekundärindex. Dessutom kan det går inte att begära en lista över anställda sorterade i en annan ordning än **RowKey** ordning.  

Du förutse en mycket stor volym med transaktioner mot dessa enheter och vill minska risken för tabelltjänsten begränsning av klienten.  

#### <a name="solution"></a>Lösning
Undvik bristande sekundärindex, kan du lagra flera kopior av varje entitet med varje kopia med hjälp av olika **PartitionKey** och **RowKey** värden. Om du sparar en entitet med strukturer som visas nedan, kan du effektivt hämta medarbetare enheter baserat på e-postadress eller medarbetare id. Prefixet värden för den **PartitionKey**, ”empid_” och ”email_” kan du identifiera vilka index som du vill använda för en fråga.  

![][10]

Följande två filtervillkoren (en slå upp av anställnings-id och en sökning efter av e-postadress) ange både punkt frågor:  

* $filter = (PartitionKey eq ' empid_Sales') och (RowKey eq '000223')
* $filter = (PartitionKey eq ' email_Sales') och (RowKey eq 'jonesj@contoso.com')  

Om du frågar efter ett intervall med anställdas enheter du kan ange ett intervall i medarbetare id ordning eller ett intervall som är sorterad i e-postadress ordning genom att fråga om entiteter med ett prefix i den **RowKey**.  

* Hitta alla anställda på försäljningsavdelningen med medarbetare id i intervallet **000100** till **000199** sorteras medarbetare id ordning används: $filter = (PartitionKey eq ' empid_Sales') och (RowKey ge '000100') och (RowKey le '000199')  
* Hitta alla anställda på försäljningsavdelningen med en e-postadress som börjar med ”a” i e-postadress ordning användning: $filter = (PartitionKey eq ' email_Sales') och (RowKey ge ”a”) och (RowKey lt ”b”)  

Observera att filtersyntaxen som används i exemplen ovan är från tabelltjänsten REST API för mer information finns i [fråga entiteter](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du kan behålla din dubbla entiteter överensstämmelse med varandra med hjälp av den [överensstämmelse transaktioner mönster](#eventually-consistent-transactions-pattern) att underhålla de primära och sekundära index entiteterna.  
* Table storage är relativt billig använder så overhead kostnaden för lagring av duplicerade data inte får vara ett större problem. Du bör dock alltid utvärdera kostnaden för din design utifrån din förväntade lagringsbehov och bara lägga till dubbla entiteter för att stödja frågorna client-program körs.  
* Det värde som används för den **RowKey** måste vara unikt för varje entitet. Överväg att använda sammansatta nyckelvärden.  
* Utfyllnad numeriska värden i den **RowKey** (exempelvis anställnings-id 000223), aktiverar korrigera sortera och filtrera baserat på övre och nedre gränser.  
* Du behöver inte nödvändigtvis att kopiera alla egenskaper för entiteten. Till exempel om frågorna som sökning entiteter med den e-posten-adressen i den **RowKey** behöver aldrig medarbetarens ålder, dessa enheter kan ha följande struktur:
  
  ![][11]
* Det är vanligtvis bättre att lagra duplicerade data och se till att du kan hämta alla data som du behöver med en enskild fråga att använda en fråga för att hitta en entitet med sekundärt index och en annan att söka efter data som krävs i det primära indexet än.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när klientprogrammet måste hämta entiteter med olika nycklar olika när klienten behöver hämta entiteter i olika sorteringsordningar, och där du kan identifiera varje entitet med olika unika värden. Använd det här mönstret när du vill undvika överstiger skalbarhetsgränser partition när du utför entitet sökningar med hjälp av de olika **RowKey** värden.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Mönster för överensstämmelse transaktioner](#eventually-consistent-transactions-pattern)  
* [Intra-partition sekundärt index mönster](#intra-partition-secondary-index-pattern)  
* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Entiteten gruppera transaktioner](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Mönster för överensstämmelse transaktioner
Aktivera överensstämmelse beteende mellan partitionsgränser eller lagring system gränser med hjälp av Azure köer.  

#### <a name="context-and-problem"></a>Kontext och problem
EGTs aktivera atomiska transaktioner mellan flera enheter som delar samma partitionsnyckel. För bättre prestanda och skalbarhet som du kan välja att lagra entiteter som har konsekvenskontroll krav i separata partitioner eller i ett separat lagringssystem: i ett sådant scenario, du kan inte använda EGTs för att upprätthålla enhetliga. Du kan till exempel ha ett krav att underhålla slutliga konsekvensen mellan:  

* Entiteter som lagras i två olika partitioner i samma tabell, i olika tabeller eller i olika lagringskonton.  
* En entitet som lagras i tabelltjänsten och en blob som lagras i Blob-tjänsten.  
* En entitet som lagras i tabelltjänsten och en fil i ett filsystem.  
* En entitet arkivet i tabelltjänsten indexerat ännu med Azure Search-tjänsten.  

#### <a name="solution"></a>Lösning
Du kan implementera en lösning som ger slutliga konsekvensen mellan två eller flera partitioner lagringssystem med hjälp av Azure köer.
För att illustrera den här metoden förutsätter att du har ett krav för att kunna arkivera gamla medarbetare enheter. Den gamla medarbetare enheter frågas sällan och bör undantas från alla aktiviteter som handlar om aktuella anställda. För att implementera det här kravet du lagrar aktiva medarbetare i den **aktuella** tabell och tidigare anställda i den **Arkiv** tabell. Arkivering av en medarbetare måste du ta bort entiteten från den **aktuella** tabell och lägga till enheten som den **Arkiv** , men du kan inte använda en EGT för att utföra dessa två åtgärder. Arkivåtgärden måste vara överensstämmelse för att undvika risken för att ett fel som orsakar en entitet som ska visas i båda eller inget tabeller. Följande sekvensdiagram beskrivs stegen i den här åtgärden. Mer information ges för undantag sökvägar i följande text.  

![][12]

En klient initierar Arkiv igen genom att placera ett meddelande på en Azure-kö, i det här exemplet att arkivera medarbetare #456. En arbetsroll avsöker kön för nya meddelanden. När den hittar en läser meddelandet och en dold kopia finns kvar i kön. Arbetsrollen nästa hämtar en kopia av entiteten från den **aktuella** tabell, infogar en kopia i den **Arkiv** table och tar sedan bort ursprungligt från den **aktuella** tabell. Slutligen, om det inte fanns några fel från föregående steg, arbetsrollen tar bort dolda meddelandet från kön.  

I det här exemplet steg 4 infogar medarbetaren till den **Arkiv** tabell. Det gick att lägga till anställde till en blobb i Blob-tjänsten eller en fil i ett filsystem.  

#### <a name="recovering-from-failures"></a>Återställa från fel
Det är viktigt som åtgärder i steg **4** och **5** måste vara *idempotent* ifall arbetsrollen behöver starta om arkivåtgärden. Om du använder tjänsten tabellen för steget **4** bör du använda en ”infoga eller ersätta” åtgärd; för steg **5** bör du använda en ”ta bort om finns” åtgärden i klientbiblioteket som du använder. Om du använder en annan lagringssystemet, måste du använda en lämplig idempotent-åtgärd.  

Om arbetsrollen slutförs aldrig steg **6**, och sedan efter en tidsgräns meddelandet visas igen på kön för arbetsrollen att försöka bearbeta den. Worker-rollen kan kontrollera hur många gånger meddelandet i kön har läsa och, om det behövs Flagga det är ett ”skadligt” meddelande för undersökningen genom att skicka den till en särskild kö. Läs mer om läsa meddelanden i kön och kontrollera antalet dequeue [få meddelanden](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Fel från tabellen och kön tjänsterna är tillfälligt fel och ditt klientprogram ska innehålla lämplig logik för att hantera dem.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Den här lösningen ger inte för transaktionsisoleringen. Till exempel en klient kan läsa den **aktuella** och **Arkiv** tabeller när arbetsrollen mellan stegen **4** och **5**, och se en Inkonsekvent visning av data. Observera att data är konsekventa förr eller senare.  
* Du måste vara säker på att steg 4 och 5 är idempotent för att säkerställa slutliga konsekvensen.  
* Du kan skala lösningen genom att använda flera köer och worker rollinstanser.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill garantera slutliga konsekvensen mellan enheter som finns i olika partitioner eller tabeller. Du kan utöka detta mönster för att säkerställa slutliga konsekvensen för åtgärder i tabelltjänsten och Blob-tjänsten och andra Azure Storage-datakällor, till exempel databasen eller filsystemet.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Entiteten gruppera transaktioner](#entity-group-transactions)  
* [Merge eller ersätta](#merge-or-replace)  

> [!NOTE]
> Om transaktionsisoleringen är viktigt att din lösning bör du designar tabellerna så att du kan använda EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Index entiteter mönster
Underhålla index enheter om du vill aktivera effektiva sökningar som returnerar en lista över enheter.  

#### <a name="context-and-problem"></a>Kontext och problem
Tabelltjänsten indexerar automatiskt enheter med hjälp av den **PartitionKey** och **RowKey** värden. Detta gör att ett klientprogram att hämta en entitet som effektivt med en punkt-fråga. Till exempel använder tabellstrukturen som visas nedan, ett klientprogram enkelt kan hämta en enskild medarbetare entitet med id och ett avdelningsnamn (den **PartitionKey** och **RowKey**).  

![][13]

Om du vill kunna hämta en lista över anställdas enheter baserat på värdet för en annan icke-unikt egenskap, till exempel efternamn, måste du använda en mindre effektiv partition-sökning för att hitta matchningar i stället för att använda ett index för att leta upp dem direkt. Det beror på att tabelltjänsten inte ger sekundärindex.  

#### <a name="solution"></a>Lösning
Om du vill aktivera sökning efter efternamn med entiteten struktur som visas ovan, måste du upprätthålla en lista över medarbetare-ID: n. Om du vill hämta medarbetare entiteter med ett visst efternamn, till exempel Karlsson, måste du först lokalisera listan över medarbetare-ID: n för anställda med Karlsson som efternamn och hämta anställdas enheter. Det finns tre huvudsakliga alternativ för att lagra listor över medarbetare-ID: n:  

* Använda blob storage.  
* Skapa index entiteter i samma partition som anställdas enheter.  
* Skapa index entiteter i en separat partition eller tabellen.  

<u>Alternativ #1: Använda blob storage</u>  

För det första alternativet, skapar du en blob för varje unik efternamn och i varje blobstore en lista över de **PartitionKey** (avdelning) och **RowKey** värden (anställnings-id) för anställda som har det senaste namnet. När du lägger till eller ta bort en medarbetare bör du kontrollera att innehållet i den relevanta blobben är överensstämmelse med anställdas enheter.  

<u>Alternativ #2:</u> skapa index entiteter i samma partition  

För det andra alternativet, använder du index entiteter som lagrar följande information:  

![][14]

Den **EmployeeIDs** egenskapen innehåller en lista över medarbetare-ID för anställda med efternamn som lagras i den **RowKey**.  

Följande steg beskriver hur du bör följa när du lägger till en ny medarbetare om du använder det andra alternativet. I det här exemplet lägger vi till en anställd med Id 000152 och efternamn Karlsson på försäljningsavdelningen:  

1. Hämta entiteten index med en **PartitionKey** värdet ”Försäljning” och **RowKey** värdet ”Jansson”. Spara ETag för den här entiteten i steg 2.  
2. Skapa entitet grupp transaktion (det vill säga en batchåtgärd) som infogar entiteten medarbetare (**PartitionKey** värdet ”Försäljning” och **RowKey** värdet ”000152”), och uppdaterar entiteten index (**PartitionKey** värdet ”Försäljning” och **RowKey** värdet ”Jansson”) genom att lägga till den nya medarbetare-id i listan i fältet EmployeeIDs. Mer information om entiteten grupptransaktioner finns [entitet gruppera transaktioner](#entity-group-transactions).  
3. Om entiteten grupp transaktionen misslyckas på grund av ett fel för Optimistisk samtidighet (någon annan har bara ändrade entiteten index), måste du börja om från steg 1 igen.  

Du kan använda ett liknande sätt att ta bort en medarbetare om du använder andra alternativ. Ändra en anställds efternamn är lite mer komplext eftersom du behöver köra en entitet grupp transaktion som uppdaterar tre enheter: medarbetare entiteten och entiteten index för det gamla efternamnet entiteten index för det nya efternamnet. Innan du gör några ändringar för att hämta ETag-värden som du sedan kan använda för att utföra uppdateringar med hjälp av Optimistisk samtidighet måste du hämta varje entitet.  

Följande steg beskriver processen bör du följa när du behöver leta upp alla anställda med ett visst efternamn på en avdelning om du använder andra alternativ. Det här exemplet letar vi upp alla anställda med efternamn Karlsson på försäljningsavdelningen:  

1. Hämta entiteten index med en **PartitionKey** värdet ”Försäljning” och **RowKey** värdet ”Jansson”.  
2. Parsa listan över anställnings-ID i fältet EmployeeIDs.  
3. Om du behöver ytterligare information om var och en av dessa anställda (till exempel sina e-postadresser) hämtar var och en av anställdas enheter med hjälp av **PartitionKey** värdet ”Försäljning” och **RowKey** värden från den lista över anställda som du hämtade i steg 2.  

<u>Alternativet #3:</u> skapa index entiteter i en separat partition eller tabell  

Det tredje alternativet Använd i index entiteter som lagrar följande information:  

![][15]

Den **EmployeeIDs** egenskapen innehåller en lista över medarbetare-ID för anställda med efternamn som lagras i den **RowKey**.  

Du kan inte använda EGTs med det tredje alternativet för att upprätthålla enhetliga eftersom indexet entiteter i en separat partition från medarbetare entiteter. Du bör kontrollera att index entiteter är överensstämmelse med medarbetare entiteter.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Denna lösning kräver minst två frågor för att hämta matchande entiteter: en för att fråga index entiteter för att hämta listan över **RowKey** värden och frågor för att hämta varje entitet i listan.  
* Med hänsyn till att en enskild entitet har en maximal storlek på 1 MB, förutsätter #2 och 3 # i lösningen att lista över medarbetare-ID för alla angivna efternamn aldrig är större än 1 MB. Om listan över medarbetare-ID: n är sannolikt måste vara större än 1 MB i storlek, Använd alternativet #1 och lagra indexinformationen i blob storage.  
* Om du använder alternativet #2 måste (med EGTs som hanterar att lägga till och ta bort anställda och ändra en anställds efternamn) du utvärdera om volymen av transaktioner, kommer hanterar skalbarhetsgränser i en given partition. Om så är fallet bör du överväga en överensstämmelse lösning (#1 eller #3) som använder köer för att hantera begäranden om uppdateringar och gör det möjligt att lagra indexet-entiteter i en separat partition från medarbetare entiteter.  
* Alternativet #2 i den här lösningen förutsätter att du vill leta upp efter efternamn inom en avdelning: till exempel du vill hämta en lista över anställda med efternamn Karlsson på försäljningsavdelningen. Om du vill kunna leta upp alla anställda med efternamn Karlsson i hela organisationen använda alternativet #1 eller alternativet #3.
* Du kan implementera en lösning med kön som levererar slutliga konsekvensen (finns i [överensstämmelse transaktioner mönster](#eventually-consistent-transactions-pattern) för mer information).  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill söka efter en mängd av entiteter med samma vanliga egenskapsvärde, till exempel alla anställda med efternamn Karlsson.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Mönster för överensstämmelse transaktioner](#eventually-consistent-transactions-pattern)  
* [Entiteten gruppera transaktioner](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Denormalization mönster
Kombinera relaterade data tillsammans i en enda enhet så att du kan hämta alla data som du behöver med en enda fråga.  

#### <a name="context-and-problem"></a>Kontext och problem
I en relationsdatabas normalisera du normalt data för att ta bort duplicering, vilket resulterar i frågor som hämtar data från flera tabeller. Om du normalisera dina data i Azure-tabeller, måste du se flera sändningar fram och tillbaka från klienten till servern för att hämta relaterade data. Till exempel med tabellstrukturen nedan om du behöver två sändningar att hämta information för en avdelning: en för att hämta entiteten avdelningen som innehåller managerns-id och sedan en annan begäran om att hämta information om den manager i en medarbetare entitet.  

![][16]

#### <a name="solution"></a>Lösning
I stället för att lagra data i två separata entiteterna, denormalize data och behålla en kopia av den manager information i entiteten avdelning. Exempel:  

![][17]

Du kan nu hämta allt du behöver om en avdelning med en punkt-fråga med avdelning entiteter som lagras med dessa egenskaper finns.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Det finns en kostnad som associeras med att lagra vissa data två gånger. Prestandafördelarna (följd färre begäranden till lagringstjänsten) vanligtvis uppväger marginell ökningen lagringskostnader (och kostnaden är delvis förskjuten genom en minskning av antal transaktioner som du behöver för att hämta information om en avdelning ).  
* Du måste upprätthålla konsekvensen för de två entiteter som lagrar information om chefer. Du kan hantera konsekvenskontroll problemet med hjälp av EGTs för att uppdatera flera entiteter i en atomisk transaktion: i det här fallet avdelning entiteten och medarbetare entiteten för avdelning manager lagras i samma partition.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du ofta behöver leta upp relaterad information. Det här mönstret minskar antalet frågor som klienten måste se till att hämta de data som krävs.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Entiteten gruppera transaktioner](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Sammansatt nyckel mönster
Använd sammansatta **RowKey** värden att aktivera en klient att söka efter relaterade data med en enda fråga.  

#### <a name="context-and-problem"></a>Kontext och problem
I en relationsdatabas är det ganska naturlig använda kopplingar i frågor för att returnera relaterade delar av data till klienten i en enskild fråga. Du kan till exempel använda anställnings-id för att leta upp en lista över relaterade entiteter som innehåller prestanda och granska data för denna medarbetare.  

Anta att du lagrar medarbetare entiteter i tabelltjänsten med följande struktur:  

![][18]

Du måste också att lagra historiska data som rör omdömen och prestanda för varje år medarbetaren har arbetat för din organisation och du behöver kunna komma åt informationen per år. Ett alternativ är att skapa en annan tabell som innehåller entiteter med följande struktur:  

![][19]

Observera att med den här metoden kan du välja att duplicera viss information (till exempel förnamn och efternamn) i den nya enheten så att du kan hämta dina data med en enskild begäran. Du kan dock behålla stark konsekvens eftersom du inte kan använda en EGT att automatiskt uppdatera två entiteter.  

#### <a name="solution"></a>Lösning
Lagra nya entitetstypen i den ursprungliga tabellen med hjälp av entiteter med följande struktur:  

![][20]

Observera hur **RowKey** är nu en sammansatt nyckel består av anställnings-id och år granska data som gör att du kan hämta den anställde prestanda och granska data med en begäran för en enda entitet.  

I följande exempel beskrivs hur du kan hämta alla data för granskning för en viss medarbetare (till exempel medarbetare 000123 på försäljningsavdelningen):  

$filter = (PartitionKey eq 'Sales') och (RowKey ge 'empid_000123') och (RowKey lt 'empid_000124') & $select = RowKey, Arbetsledarens, peer-klassificering, kommentarer  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du bör använda ett lämpligt avgränsningstecknet som gör det enkelt att parsa den **RowKey** värde: till exempel **000123_2012**.  
* Den här entiteten lagras också i samma partition som andra entiteter som innehåller relaterade data för samma medarbetare, vilket innebär att du kan använda EGTs för att underhålla stark konsekvens.
* Du bör överväga hur ofta du ska fråga efter data om det här mönstret är rätt.  Till exempel om du kommer åt data granska sällan och huvudsakliga medarbetardata ofta bör du behålla dem som separata entiteterna.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra en eller flera relaterade entiteter som du frågar ofta.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Entiteten gruppera transaktioner](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  
* [Mönster för överensstämmelse transaktioner](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Loggen pilslut mönster
Hämta den *n* entiteter som senast lades till en partition med hjälp av en **RowKey** värde som sorterar i omvänd datum och tid ordning.  

#### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt krav är att kunna hämta de nyligen skapade enheterna, till exempel de senaste tio utgifter ansökningar som görs av en medarbetare. Tabell frågar stöd för en **$top** frågeåtgärden att returnera först *n* entiteter från en uppsättning: Ingen åtgärd har motsvarande frågan att returnera de sista n entiteterna i en mängd.  

#### <a name="solution"></a>Lösning
Lagra enheter med hjälp av en **RowKey** att naturligt sorterar i tidsvärdet i omvänd ordning med hjälp av det senaste posten är alltid den första i tabellen.  

Du kan till exempel använda en omvänd tick-värde som härletts från aktuellt datum och tid för att kunna hämta tio senaste utgifter anspråk skickas av en medarbetare. Följande C# kodexemplet visar ett sätt att skapa ett lämpligt ”inverterad tick” värde för en **RowKey** som sorterar från den senaste äldsta:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Du kan komma tillbaka till datum tid-värden med hjälp av följande kod:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Frågan för tabellen ser ut så här:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du måste fylla värdet omvänd skalstreck med inledande nollor så strängvärdet sorterar som förväntat.  
* Du måste vara medveten om skalbarhetsmål på nivån för en partition. Var noga med att inte skapa hotspot-partitioner.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver åtkomst till entiteter i tidsvärdet i omvänd ordning eller när du behöver åtkomst till de nyligen tillagda entiteterna.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Lägga / lägga till ett mönster](#prepend-append-anti-pattern)  
* [Hämta entiteter](#retrieving-entities)  

### <a name="high-volume-delete-pattern"></a>Ta bort mönster för hög volym
Aktivera borttagning av ett stort antal enheter genom att lagra alla entiteter för samtidiga borttagning i sina egna separata tabell. du tar bort entiteterna genom att ta bort tabellen.  

#### <a name="context-and-problem"></a>Kontext och problem
Många program att ta bort gamla data som inte längre behöver vara tillgängliga för ett klientprogram, eller som programmet har arkiverats till ett annat lagringsmedium. Dessa data identifieras vanligtvis med ett datum: du till exempel har ett krav för att ta bort poster för alla inloggningsbegäranden som är mer än 60 dagar.  

Ett möjligt design är att använda datum och tid för inloggningsbegäran i den **RowKey**:  

![][21]

Det här sättet undviker partition surfpunkter eftersom programmet kan infoga och ta bort inloggningen entiteter för varje användare i en separat partition. Den här metoden kan dock dyrt och tidskrävande om du har ett stort antal entiteter eftersom du måste först utför en tabellgenomsökning för att identifiera alla enheter att ta bort och du måste ta bort varje gamla entitet. Observera att du kan minska antalet sändningar till servern som krävs för att ta bort de gamla enheterna av batchbearbetning flera delete-begäranden till EGTs.  

#### <a name="solution"></a>Lösning
Använd en separat tabell för varje dag på inloggningsförsök. Du kan använda entiteten designen ovan för att undvika surfpunkter när du infogar entiteter och ta bort gamla enheter är nu bara en fråga om du tar bort en tabell varje dag (en enda Lagringsåtgärden) i stället för att hitta och ta bort hundratals och tusentals person inloggningen enheter varje dag.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Stöder din design andra sätt som programmet ska använda data, till exempel söka efter specifika enheter som länkar till andra data eller genererar samlar in information?  
* Din design undvika aktiva punkter vid infogning av nya enheter?  
* Förvänta dig en fördröjning om du vill återanvända samma namn när du tar bort den. Det är bättre att alltid använda unikt tabellnamn.  
* Räkna med vissa begränsningar när du börjar använda en ny tabell medan tabelltjänsten lär sig åtkomstmönster och distribuerar partitionerna mellan noder. Du bör överväga hur ofta du behöver skapa nya tabeller.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du har en stor volym med enheter som du måste ta bort samtidigt.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Entiteten gruppera transaktioner](#entity-group-transactions)
* [Ändra entiteter](#modifying-entities)  

### <a name="data-series-pattern"></a>Serien datamönster
Store fullständig dataserier i en enda enhet för att minimera antalet begäranden som du gör.  

#### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt scenario är för ett program att lagra en serie av data som normalt behöver hämta allt samtidigt. Programmet kan till exempel registrera hur många Snabbmeddelanden meddelanden varje anställd skickar varje timme och sedan använda informationen för att rita ut hur många meddelanden varje användare som skickas över de föregående 24 timmarna. En kan vara att lagra 24 entiteter för varje medarbetare:  

![][22]

Med den här designen kan du lätt hitta och uppdatera entitet för varje medarbetare uppdateras när programmet måste uppdatera värdet för antal meddelande. Om du vill hämta information för att rita ett diagram för aktiviteten under de föregående 24 timmarna, måste du hämta 24 entiteter.  

#### <a name="solution"></a>Lösning
Använd följande design med en separat egenskap för att lagra antalet meddelanden för varje timme:  

![][23]

Du kan använda en sammanfogning med den här designen för att uppdatera antalet meddelanden för en medarbetare för en given timme. Du kan nu hämta all information som du behöver att rita diagram med en begäran för en enda entitet.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Om fullständig dataserierna inte får plats i en enda entitet (en entitet kan ha upp till 252 egenskaper), kan du använda ett alternativt datalager, till exempel en blob.  
* Om du har flera klienter som uppdaterar en entitet samtidigt, du behöver använda den **ETag** att implementera Optimistisk samtidighet. Om du har många klienter kan uppstå det hög konkurrens.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver uppdatera och hämta en serie som är associerade med en enskild entitet.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Mönster för stora entiteter](#large-entities-pattern)  
* [Merge eller ersätta](#merge-or-replace)  
* [Överensstämmelse transaktioner mönster](#eventually-consistent-transactions-pattern) (om du lagrar dataserien i en blob)  

### <a name="wide-entities-pattern"></a>Wide entiteter mönster
Använda flera fysiska enheter för att lagra logiska entiteter med mer än 252 egenskaper.  

#### <a name="context-and-problem"></a>Kontext och problem
En enskild entitet kan ha högst 252 egenskaper (exklusive obligatoriska Systemegenskaper) och kan inte lagra mer än 1 MB data totalt. I en relationsdatabas, skulle du normalt får avrunda några gränser för storleken på en rad att lägga till en ny tabell och framtvinga en 1-till-1-relation mellan dem.  

#### <a name="solution"></a>Lösning
Du kan använda tabelltjänsten för att lagra flera entiteter som representerar ett enda stort företag objekt med mer än 252 egenskaper. Om du vill spara en uppräkning av antalet IM-meddelanden som skickas av medarbetaren för de senaste 365 dagarna kan du använda följande designen som använder två entiteter med olika scheman:  

![][24]

Du kan använda en EGT om du behöver göra en ändring som kräver uppdatering båda entiteter för att hålla dem synkroniserade med varandra. Annars kan du använda en enda merge-operation för att uppdatera antalet meddelanden för en viss dag. För att hämta alla data för en enskild medarbetare måste du hämta båda enheter som du kan göra med två effektivt begäranden som använder både ett **PartitionKey** och en **RowKey** värde.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Hämtar en fullständig logisk entitet omfattar minst två lagringstransaktioner: en för att hämta varje fysisk entitet.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när behöver lagra enheter vars storlek eller ett antal egenskaper som överskrider gränserna för en enskild entitet i tabelltjänsten.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Entiteten gruppera transaktioner](#entity-group-transactions)
* [Merge eller ersätta](#merge-or-replace)

### <a name="large-entities-pattern"></a>Mönster för stora entiteter
Använda blob storage för att lagra stora egenskapsvärden.  

#### <a name="context-and-problem"></a>Kontext och problem
En enskild entitet kan inte lagra mer än 1 MB data totalt. Om en eller flera av dina egenskaper lagrar värden som orsakar den totala storleken på din enhet överskrider detta värde kan lagra du inte hela entiteten i tabelltjänsten.  

#### <a name="solution"></a>Lösning
Om entiteten överskrider 1 MB i storlek eftersom en eller flera egenskaper innehåller stora mängder data, du lagra data i Blob-tjänsten och sedan lagra den blob-adressen i en egenskap i entiteten. Du kan till exempel lagra foto av en medarbetare i blob storage och lagra en länk till bilden i den **foto** egenskap för dina medarbetare entitet:  

![][25]

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Använda eventuell enhetliga mellan entiteten i tabelltjänsten och data i Blob-tjänsten på [överensstämmelse transaktioner mönster](#eventually-consistent-transactions-pattern) att underhålla din entiteter.
* Hämtar en fullständig entitet omfattar minst två lagringstransaktioner: en för att hämta entiteten och en för att hämta blob-data.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra enheter vars storlek överskrider gränserna för en enskild entitet i tabelltjänsten.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Mönster för överensstämmelse transaktioner](#eventually-consistent-transactions-pattern)  
* [Wide entiteter mönster](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Lägga/lägga till ett mönster
Öka skalbarheten när du har en stor volym med infogningar genom att sprida infogningarna över flera partitioner.  

#### <a name="context-and-problem"></a>Kontext och problem
Tillagt eller lägger till enheter till din lagrade entiteter vanligtvis resulterar i programmet att lägga till nya entiteter till den första eller sista partitionen i en sekvens av partitioner. I så fall måste äger alla infogningar vid något tillfälle rum i samma partition, skapar en hotspot som förhindrar tabelltjänsten från belastningen belastningsutjämning infogningar över flera noder och vilket kan orsaka att träffa skalbarhetsmål för programmet partition. Till exempel om du har ett program som loggar nätverks- och åtkomst av anställda, sedan en entitet struktur som visas nedan kan resultera i den aktuella timman partition blir en hotspot om mängden transaktioner når skalbarhet mål för en enskilda partition:  

![][26]

#### <a name="solution"></a>Lösning
Följande alternativ entitet struktur förhindrar en hotspot på en viss partition som programmet loggar händelser:  

![][27]

Meddelande med det här exemplet hur både den **PartitionKey** och **RowKey** är sammansatta nycklar. Den **PartitionKey** använder både avdelning och medarbetare id för att distribuera loggning över flera partitioner.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Stöder den alternativa nyckelstruktur som undviker att skapa varm partitioner på infogningar effektivt frågorna klientprogrammet gör?  
* Innebär förväntade volymen av transaktioner som troligen kommer att nå skalbarhetsmål för en enskild partition och begränsas av lagringstjänsten?  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Undvik prepend/lägga till ett mönster när volymen av transaktioner kan leda till begränsning av lagringstjänsten när du använder en varm partition.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Loggen pilslut mönster](#log-tail-pattern)  
* [Ändra entiteter](#modifying-entities)  

### <a name="log-data-anti-pattern"></a>Loggen anti datamönster
Normalt ska du använda Blob-tjänsten i stället för tabelltjänsten för att lagra loggdata.  

#### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt användningsfall för loggdata är att hämta en uppsättning loggposter för ett visst datum/tid-intervall: till exempel du vill hitta alla fel och kritiska meddelanden som tillämpningsprogrammet loggade mellan 15:04 och 15:06 på ett visst datum. Du inte vill använda datum och tid för loggmeddelandet är för att avgöra partitionen du sparar logg entiteter till: som resulterar i en varm partition eftersom samtidigt, kommer alla entiteter i loggen delar samma **PartitionKey** värde (finns i avsnittet [Prepend/lägga till ett mönster](#prepend-append-anti-pattern)). Följande entitet schemat för ett loggmeddelande resulterar i en varm partition eftersom programmet skriver alla loggmeddelanden till partitionen för den aktuella datum och tid:  

![][28]

I det här exemplet i **RowKey** innehåller datum och tid för loggmeddelande så att loggmeddelanden lagras i tidsvärdet ordning och innehåller ett meddelande-id om flera loggmeddelanden delar samma datum och tid.  

En annan metod är att använda en **PartitionKey** som säkerställer att programmet skriver meddelanden över ett antal partitioner. Om källan för loggmeddelandet erbjuder ett sätt att distribuera meddelanden mellan många partitioner, kan du använda följande entitet schema:  

![][29]

Problem med det här schemat är dock att om du vill hämta alla loggmeddelanden för ett visst tidsintervall måste du söka varje partition i tabellen.

#### <a name="solution"></a>Lösning
I föregående avsnitt markerade problemet med försöker använda tabelltjänsten för att lagra loggposter och föreslagna två otillräckliga, Designer. En lösning som ledde till en varm partition med risk för prestandaproblem skriva loggmeddelanden; andra lösningen resulterade i dåliga prestanda på grund av kravet att söka igenom varje partition i tabellen för att hämta loggmeddelanden för ett visst tidsintervall. BLOB storage erbjuder en bättre lösning för den här typen av scenario och detta är hur Azure Storage Analytics lagrar loggdata som samlas in.  

Det här avsnittet beskrivs hur Storage Analytics lagrar loggdata i blob storage som en illustration av den här metoden för att lagra data som du vanligtvis fråga med intervallet.  

Storage Analytics lagrar meddelanden i en avgränsad format i flera blobbar. Avgränsad format gör det enkelt för ett klientprogram att tolka data i loggmeddelandet.  

Storage Analytics använder en namngivningskonvention för blobbar som gör det möjligt för dig att hitta blob (eller BLOB) som innehåller loggmeddelanden som du söker. Till exempel innehåller en blob med namnet ”queue/2014/07/31/1800/000001.log” loggmeddelanden som relaterar till Kötjänsten för timme som börjar vid 18:00 31 juli 2014. ”000001” anger att detta är den första loggfilen för den här perioden. Tidsstämplar i de första och sista loggmeddelanden som lagras i filen som en del av den blobbens metadata även information om Storage Analytics. API för blob storage kan du hitta blobbar i en behållare baserat på ett namnprefix: Om du vill hitta alla blobbar som innehåller kön loggdata för timme som börjar vid 18:00, du kan använda prefixet ”kö/2014/07/31/1800”.  

Storage Analytics buffertar loggmeddelanden internt och regelbundet uppdaterar lämpliga blob eller skapar en ny loggposter senaste batchen. Detta minskar antalet skrivningar som måste utföras på blob-tjänsten.  

Om du implementerar en liknande lösning i ditt eget program, måste du hantera kompromissen mellan tillförlitlighet (skriver varje loggpost till blob-lagring när den uppstår) och kostnad och skalbarhet (buffring uppdateringar i programmet och skrivning dem till blob storage i batchar).  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du lagrar loggdata:  

* Om du skapar en tabelldesign som undviker potentiella hot partitioner kan hända att du inte kommer åt din loggdata effektivt.  
* Om du vill bearbeta loggdata måste ofta en klient att läsa in många poster.  
* Även om loggdata är ofta strukturerad, kan blob-lagring vara en bättre lösning.  

### <a name="implementation-considerations"></a>Att tänka på vid implementering
Det här avsnittet beskrivs några av övervägandena att ha i åtanke när du implementerar de mönster som beskrivs i föregående avsnitt. De flesta av det här avsnittet använder exempel skrivna i C# som använder Storage-klientbiblioteket (version 4.3.0 vid tidpunkten för skrivning).  

### <a name="retrieving-entities"></a>Hämta entiteter
Enligt beskrivningen i avsnittet [Design för frågor](#design-for-querying), de mest effektiva frågan är en punkt-fråga. I vissa fall kan du dock behöva hämta flera entiteter. Det här avsnittet beskrivs några vanliga sätt att hämta entiteter med Storage-klientbiblioteket.  

#### <a name="executing-a-point-query-using-the-storage-client-library"></a>Köra en punkt-fråga med Storage-klientbibliotek
Det enklaste sättet att köra en fråga om platsen är att använda den **hämta** tabell åtgärden som visas i följande C# kodavsnitt som hämtar en entitet med en **PartitionKey** värdets ”försäljning” och en  **RowKey** värdets ”212”:  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Observera hur entiteten förväntar att det här exemplet hämtas för att vara av typen **EmployeeEntity**.  

#### <a name="retrieving-multiple-entities-using-linq"></a>Hämtning av flera enheter med hjälp av LINQ
Du kan hämta flera entiteter med hjälp av LINQ med Storage-klientbiblioteket och ange en fråga med en **där** satsen. För att undvika en tabellgenomsökning, bör du alltid skriva den **PartitionKey** värde i where-satsen, och om möjligt den **RowKey** värdet för att undvika tabell och partition genomsökningar. Tabelltjänsten stöder en begränsad uppsättning jämförelseoperatorer (större än, större än eller lika med, mindre än, mindre än eller lika med, lika och inte lika) ska användas i where-satsen. Följande C# kodfragment hittar alla anställda vars efternamn börjar med ”B” (förutsatt att den **RowKey** lagrar efternamn) på försäljningsavdelningen (förutsatt att den **PartitionKey** lagrar den avdelningsnamn):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Observera hur frågan anger både en **RowKey** och en **PartitionKey** att säkerställa bättre prestanda.  

Följande kodexempel visar hur motsvarande fungerar med flytande API (Mer information om flytande API: er i allmänhet finns [bästa praxis för att designa en flytande API](http://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> Exemplet omsluter flera **CombineFilters** metoder för att inkludera tre filtervillkor.  
> 
> 

#### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Hämtar stort antal enheter från en fråga
En optimal frågan returnerar en enskild entitet baserat på en **PartitionKey** värde och ett **RowKey** värde. Du kan dock ha ett krav för att returnera många entiteter från samma partition eller även många partitioner i vissa scenarier.  

Alltid fullständigt bör du testa prestanda för ditt program i dessa scenarier.  

En fråga mot tabelltjänsten kan returnera högst 1 000 enheter på en gång och kan utföra för högst fem sekunder. Om resultatet innehåller fler än 1 000 enheter om frågan inte slutfördes inom fem sekunder, eller om frågan överskrider gränsen partition, returnerar tabelltjänsten en fortsättningstoken om du vill aktivera klientprogrammet begär en uppsättning enheter. Mer information om hur fortsättning tokens arbete finns [Timeout för fråga och sidbrytning](http://msdn.microsoft.com/library/azure/dd135718.aspx).  

Om du använder Storage-klientbiblioteket hantera den automatiskt fortsättning token för dig som returnerar entiteter från tabelltjänsten. Följande C# kodexempel använder Storage-klientbiblioteket automatiskt hanterar fortsättning token om tabelltjänsten returnerar dem i ett svar:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

Följande C#-kod hanterar uttryckligen fortsättning token:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Du kan styra när programmet hämtar nästa segment av data med hjälp av fortsättning token explicit. Till exempel om ditt klientprogram kan du bläddra igenom de entiteter som lagras i en tabell, en användare behöver inte bläddra igenom alla entiteter som hämtas av frågan, så att programmet bara använda en fortsättningstoken för att hämta nästa segment när användaren hade slutförts bläddring genom alla entiteter i det aktuella segmentet. Den här metoden har flera fördelar:  

* Du kan begränsa mängden data som ska hämtas från tabelltjänsten och att du flyttar över nätverket.  
* Det kan du utföra asynkrona i/o i .NET.  
* Det gör att du kan serialisera fortsättningstoken till permanent lagringsutrymme så att du kan fortsätta om ett program kraschar.  

> [!NOTE]
> En fortsättningstoken returnerar vanligtvis ett segment som innehåller 1 000 enheter, även om det kan vara färre. Detta gäller även om du begränsar antalet poster som en fråga som returnerar med hjälp av **ta** att returnera de första n entiteter som matchar sökvillkoren sökning: tabelltjänsten kan returnera ett segment som innehåller färre än n entiteter tillsammans med en fortsättningstoken så att du kan hämta de återstående enheterna.  
> 
> 

Följande C#-kod visar hur du ändrar antal entiteter som returneras i ett segment:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projektion av serversidan
En enda entitet kan ha upp till 255 egenskaper och upp till 1 MB i storlek. När du frågar tabellen och hämta entiteter kanske inte behöver alla egenskaper och kan undvika att överföra data i onödan (om du vill minska svarstiden och kostnaden). Du kan använda serversidan projektion Överför bara de egenskaper som du behöver. I följande exempel hämtar bara den **e-post** egenskapen (tillsammans med **PartitionKey**, **RowKey**, **tidsstämpel**, och **ETag**) från de enheter som väljs av frågan.  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Observera hur **RowKey** värdet är tillgängligt även om den inte ingår i listan över egenskaper som hämtas.  

### <a name="modifying-entities"></a>Ändra entiteter
Storage-klientbiblioteket kan du ändra din entiteter som lagras i tabelltjänsten genom att lägga till, ta bort och uppdatera entiteter. Du kan använda EGTs batch flera insert-, update- och delete-åtgärder tillsammans för att minska antalet sändningar som krävs och förbättra prestandan för din lösning.  

Observera att undantag när Storage-klientbiblioteket utför en EGT vanligtvis innehåller index för den entitet som orsakade batch misslyckas. Detta är användbart när du felsöker kod som använder EGTs.  

Du bör också övervägas hur din design påverkar hur ditt klientprogram hanterar samtidighet och update-åtgärder.  

#### <a name="managing-concurrency"></a>Hantera samtidighet
Som standard tabelltjänsten implementerar Optimistisk samtidighet kontrollerar på nivån för enskilda entiteter för **infoga**, **sammanfoga**, och **ta bort** åtgärder, även om den är möjligt för en klient att tvinga tabelltjänsten att kringgå de här kontrollerna. Mer information om hur tabelltjänsten hanterar samtidighet finns [hantera samtidighet i Microsoft Azure Storage](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Merge eller ersätta
Den **ersätta** metod för den **TableOperation** klassen ersätter alltid fullständiga entiteten i tabelltjänsten. Om du inte använder en egenskap i begäran när egenskapen finns i entiteten lagrade bort begäran egenskapen från den lagrade entiteten. Om du inte vill ta bort en egenskap från en lagrad entitet explicit, måste du inkludera varje egenskap i begäran.  

Du kan använda den **sammanfoga** metod för den **TableOperation** klassen för att minska mängden data som du skickar till tabelltjänsten när du vill uppdatera en entitet. Den **sammanfoga** metoden ersätter alla egenskaper i entiteten lagrade med egenskapsvärden från det entitet som ingår i denna begäran, men lämnar kvar alla egenskaper i entiteten lagrade som inte ingår i begäran. Detta är användbart om du har stora entiteter och behöver bara uppdatera ett litet antal egenskaper i en begäran.  

> [!NOTE]
> Den **ersätta** och **sammanfoga** metoder misslyckas om entiteten inte finns. Alternativt kan du använda den **InsertOrReplace** och **InsertOrMerge** metoder som skapar en ny entitet om den inte finns.  
> 
> 

### <a name="working-with-heterogeneous-entity-types"></a>Arbeta med heterogena entitetstyper
Tabelltjänsten är en *schemat mindre* tabell butik som innebär att en enskild tabell kan lagra entiteter av flera typer som ger stor flexibilitet i din design. I följande exempel visas en tabell som lagrar både anställda och avdelningen enheter:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Tidsstämpel</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Förnamn</th>
<th>Efternamn</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Förnamn</th>
<th>Efternamn</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Avdelningsnamn</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Förnamn</th>
<th>Efternamn</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Observera att varje entitet måste dock ha **PartitionKey**, **RowKey**, och **tidsstämpel** värden, men kan ha en uppsättning egenskaper. Det finns inget att ange vilken typ av en enhet om du väljer att lagra informationen någonstans. Det finns två alternativ för att identifiera enhetstyp:  

* Lägga till typen i **RowKey** (eller eventuellt den **PartitionKey**). Till exempel **EMPLOYEE_000123** eller **DEPARTMENT_SALES** som **RowKey** värden.  
* Använda en separat egenskap för att registrera entitetstypen som visas i tabellen nedan.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Tidsstämpel</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Förnamn</th>
<th>Efternamn</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Medarbetare</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Förnamn</th>
<th>Efternamn</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Medarbetare</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Avdelningsnamn</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Avdelning</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Förnamn</th>
<th>Efternamn</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Medarbetare</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Det första alternativet, prepending entiteten typ till den **RowKey**, är användbart om det finns en risk att två entiteter med olika typer kan ha samma nyckelvärde. Dessutom grupperas entiteter av samma typ samman i partitionen.  

De metoder som beskrivs i det här avsnittet är särskilt relevanta till diskussionen [arvsrelationer](#inheritance-relationships) i den här guiden i avsnittet [modellering relationer](#modelling-relationships).  

> [!NOTE]
> Du bör överväga att använda ett versionsnummer i entiteten TYPVÄRDE att klientprogram att utvecklas POCO objekt och arbeta med olika versioner.  
> 
> 

Resten av det här avsnittet beskrivs några av de funktioner i Storage-klientbiblioteket som underlättar arbetet med flera typer av enheter i samma tabell.  

#### <a name="retrieving-heterogeneous-entity-types"></a>Hämta heterogena entitetstyper
Om du använder Storage-klientbiblioteket finns det tre alternativ för att arbeta med flera typer av enheter.  

Om du vet vilken typ av enhet som lagras med ett visst **RowKey** och **PartitionKey** värden, sedan kan du ange entitetstypen när du hämta entiteten som visas i de föregående två exemplen som Hämta entiteter av typen **EmployeeEntity**: [körs en punkt-fråga med Storage-klientbiblioteket](#executing-a-point-query-using-the-storage-client-library) och [hämtning av flera enheter med hjälp av LINQ](#retrieving-multiple-entities-using-linq).  

Det andra alternativet är att använda den **DynamicTableEntity** typ (en egenskapsuppsättning) i stället för en konkret POCO entitetstyp (det här alternativet kan också förbättra prestanda eftersom det finns inget behov av att serialisera och deserialisera entiteten till .NET-typer). Följande C#-kod potentiellt hämtar flera entiteter med olika typer från tabellen, men returnerar alla entiteter som **DynamicTableEntity** instanser. Därefter använder den **EntityType** egenskapen fastställa typen av varje entitet:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Observera att om du vill hämta andra egenskaper måste du använda den **TryGetValue** -metoden i den **egenskaper** -egenskapen för den **DynamicTableEntity** klass.  

Ett tredje alternativ är att kombinera med hjälp av den **DynamicTableEntity** typ och en **EntityResolver** instans. På så sätt kan du matcha flera POCO-typer i samma fråga. I det här exemplet i **EntityResolver** ombud använder den **EntityType** egenskapen att skilja mellan de två typerna av entitet som frågan returnerar. Den **lösa** metoden använder den **matcharen** ombud för att lösa **DynamicTableEntity** instanser till **TableEntity** instanser.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

#### <a name="modifying-heterogeneous-entity-types"></a>Ändra heterogena entitetstyper
Du behöver inte vet vilken typ av en entitet att ta bort den och du vet alltid vilken typ av en enhet när du infogar den. Du kan dock använda **DynamicTableEntity** typen för att uppdatera en entitet utan att känna till dess typ och utan att använda en POCO-entitetsklass. Följande kodexempel hämtas en enda entitet och kontrollerar de **EmployeeCount** egenskapen finns innan den uppdateras.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

### <a name="controlling-access-with-shared-access-signatures"></a>Kontrollera åtkomst med signaturer för delad åtkomst
Du kan använda delade signatur åtkomst (SAS)-tokens för att aktivera klientprogram att ändra (och fråga) tabellentiteter direkt utan att behöva autentisera direkt med tabelltjänsten. Det finns vanligtvis tre huvudsakliga fördelar med SAS i ditt program:  

* Du behöver inte distribuera din lagringskontonyckel till en osäker plattform (till exempel en mobil enhet) för att tillåta enheten för att komma åt och ändra entiteter i tabelltjänsten.  
* Du kan avlasta del av arbetet som webb-och arbetsroller utför när du ska hantera dina enheter till klientenheter, till exempel slutanvändarnas datorer och mobila enheter.  
* Du kan tilldela en begränsad och tid begränsad uppsättning behörigheter till en klient (till exempel tillåta att skrivskyddad åtkomst till specifika resurser).  

Läs mer om hur du använder SAS-token med tabelltjänsten [med delad åtkomst signaturer (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Du måste dock fortfarande skapa SAS-token som ger ett klientprogram för entiteter i tabelltjänsten: du bör göra detta i en miljö som har säker åtkomst till dina nycklar för lagringskonto. Normalt använder du en webb- eller arbetarroll roll för att generera SAS-token och skicka dem till klientprogram som behöver åtkomst till dina enheter. Eftersom det finns fortfarande en arbetet med genererar och leverera SAS-token till klienter, bör du hur du bäst för att minska den här kostnader, särskilt i stora mängder.  

Det är möjligt att generera en SAS-token som ger åtkomst till en delmängd av entiteter i en tabell. Som standard kan du skapa en SAS-token för en hel tabell, men det är också möjligt att ange att SAS-token som ger åtkomst till antingen en mängd **PartitionKey** värden eller en uppsättning **PartitionKey** och **RowKey** värden. Du kan välja att generera SAS-token för enskilda användare av systemet så att varje användares SAS-token endast ger dem åtkomst till sina egna enheter i tabelltjänsten.  

### <a name="asynchronous-and-parallel-operations"></a>Asynkron och parallella åtgärder
Om dina begäranden sprids över flera partitioner, kan du förbättra genomflöde och klienten svarstider med hjälp av asynkron eller parallella frågor.
Du kan till exempel ha två eller flera worker rollinstanser åtkomst till dina tabeller parallellt. Du kan har enskilda arbetsroller ansvarar för viss uppsättningar av partitioner eller helt enkelt ha flera worker rollinstanser, varje tillgång till alla partitioner i en tabell.  

Inom en klientinstans, kan du förbättra genomflöde genom att köra lagringsåtgärder asynkront. Storage-klientbiblioteket gör det enkelt att skriva asynkrona frågor och ändringar. Du kan till exempel starta med synkron metod som hämtar alla entiteter i en partition som visas i följande C#-kod:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

Du kan enkelt ändra den här koden så att frågan körs asynkront på följande sätt:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

I det här asynkron exemplet ser du följande ändringar från synkron version:  

* Metodsignaturen innehåller nu den **asynkrona** modifierare och returnerar ett **aktivitet** instans.  
* I stället för att anropa den **ExecuteSegmented** metod för att hämta resultat, metoden nu anropar den **ExecuteSegmentedAsync** metod och använder den **await** modifierare till Hämta resultat asynkront.  

Klientprogrammet kan anropa metoden flera gånger (med olika värden för den **avdelning** parametern), och varje fråga som körs på en separat tråd.  

Observera att det finns inga asynkrona versionen av den **kör** metod i den **TableQuery** klassen eftersom den **IEnumerable** gränssnittet stöder inte asynkron uppräkning.  

Du kan infoga, uppdatera och ta bort entiteter asynkront. Följande C#-exempel visar en enkel, synkron metod för att infoga eller ersätta en medarbetare entitet:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Du kan enkelt ändra den här koden så att uppdateringen körs asynkront på följande sätt:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

I det här asynkron exemplet ser du följande ändringar från synkron version:  

* Metodsignaturen innehåller nu den **asynkrona** modifierare och returnerar ett **aktivitet** instans.  
* I stället för att anropa den **kör** metod för att uppdatera entiteten metoden nu anropar den **ExecuteAsync** metoden och använder den **await** modifieraren att hämta resultat asynkront.  

Klientprogrammet kan anropa flera asynkrona metoder som det här och varje metodanropet körs på en separat tråd.  

### <a name="credits"></a>Krediter
Vi vill tacka följande medlemmar i Azure-teamet för deras bidrag: Dominic Betts, Jason Hogg, Jean Ghanem, Jai Haridas, Jeff Irwin, Vamshidhar Kommineni, Vinay Shah och Serdar Ozler samt Tom Hollander från Microsoft DX. 

Vi skulle också vilja Tack på följande Microsoft MVPS för deras feedback om värdefulla vid granskning tillfällen: Igor Papirov och Edward Bakker.

[1]: ./media/storage-table-design-guide/storage-table-design-IMAGE01.png
[2]: ./media/storage-table-design-guide/storage-table-design-IMAGE02.png
[3]: ./media/storage-table-design-guide/storage-table-design-IMAGE03.png
[4]: ./media/storage-table-design-guide/storage-table-design-IMAGE04.png
[5]: ./media/storage-table-design-guide/storage-table-design-IMAGE05.png
[6]: ./media/storage-table-design-guide/storage-table-design-IMAGE06.png
[7]: ./media/storage-table-design-guide/storage-table-design-IMAGE07.png
[8]: ./media/storage-table-design-guide/storage-table-design-IMAGE08.png
[9]: ./media/storage-table-design-guide/storage-table-design-IMAGE09.png
[10]: ./media/storage-table-design-guide/storage-table-design-IMAGE10.png
[11]: ./media/storage-table-design-guide/storage-table-design-IMAGE11.png
[12]: ./media/storage-table-design-guide/storage-table-design-IMAGE12.png
[13]: ./media/storage-table-design-guide/storage-table-design-IMAGE13.png
[14]: ./media/storage-table-design-guide/storage-table-design-IMAGE14.png
[15]: ./media/storage-table-design-guide/storage-table-design-IMAGE15.png
[16]: ./media/storage-table-design-guide/storage-table-design-IMAGE16.png
[17]: ./media/storage-table-design-guide/storage-table-design-IMAGE17.png
[18]: ./media/storage-table-design-guide/storage-table-design-IMAGE18.png
[19]: ./media/storage-table-design-guide/storage-table-design-IMAGE19.png
[20]: ./media/storage-table-design-guide/storage-table-design-IMAGE20.png
[21]: ./media/storage-table-design-guide/storage-table-design-IMAGE21.png
[22]: ./media/storage-table-design-guide/storage-table-design-IMAGE22.png
[23]: ./media/storage-table-design-guide/storage-table-design-IMAGE23.png
[24]: ./media/storage-table-design-guide/storage-table-design-IMAGE24.png
[25]: ./media/storage-table-design-guide/storage-table-design-IMAGE25.png
[26]: ./media/storage-table-design-guide/storage-table-design-IMAGE26.png
[27]: ./media/storage-table-design-guide/storage-table-design-IMAGE27.png
[28]: ./media/storage-table-design-guide/storage-table-design-IMAGE28.png
[29]: ./media/storage-table-design-guide/storage-table-design-IMAGE29.png


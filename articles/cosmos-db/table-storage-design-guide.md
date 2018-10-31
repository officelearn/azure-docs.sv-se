---
title: Guide för utformning av Azure Storage-Table | Microsoft Docs
description: Utforma skalbara och högpresterande tabeller i Azure Table Storage
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: na
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: sngun
ms.openlocfilehash: 2af93d149948071f78d0c684b812e84fa68db341
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50251132"
---
# <a name="azure-storage-table-design-guide-designing-scalable-and-performant-tables"></a>Guide för utformning av Azure Storage-tabell: Utforma skalbara och högpresterande tabeller
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Att utforma skalbara och högpresterande tabeller som du måste tänka på ett antal faktorer, till exempel prestanda, skalbarhet och kostnad. Om du har tidigare designat scheman för relationsdatabaser, dessa överväganden blir du känna igen, men det finns vissa likheter mellan Azure Table storage modell och relationella modeller, men det finns också många viktiga skillnader. Dessa skillnader vanligtvis leda till olika konstruktionerna som kan se krånglig eller fel till någon som är bekant med relationsdatabaser, men som gör bra uppfattning om du designar för ett NoSQL nyckel/värde-lager, till exempel Azure Table service. Många av din design skillnader avspeglar det faktum att Table service är utformad för att stödja molskalningsprogram som kan innehålla entiteter (rader i en relationsdatabas terminologi) av data eller för datauppsättningar som måste ha stöd för hög transaktion i volymer: därför måste du tänka på olika sätt hur du lagrar dina data och förstå hur Table service fungerar. En väl utformad NoSQL-databas kan aktivera din lösning för att skala mycket ytterligare (och till en lägre kostnad) än en lösning som använder en relationsdatabas. Den här guiden hjälper dig med de här avsnitten.  

## <a name="about-the-azure-table-service"></a>Om Azure Table service
Det här avsnittet beskrivs några av de viktigaste funktionerna i Table service som är särskilt relevanta till utformning för prestanda och skalbarhet. Om du är nybörjare på Azure Storage och Table service, läser du först [introduktion till Microsoft Azure Storage](../storage/common/storage-introduction.md) och [Kom igång med Azure Table Storage med hjälp av .NET](table-storage-how-to-use-dotnet.md) innan du läser resten av den här artikeln . Även om den här guiden fokuserar på är på Table service, innehåller den en beskrivning av de Azure-kö och Blob-tjänsterna och hur du kan använda dem tillsammans med Table service i en lösning.  

Vad är tabelltjänsten? Som förväntat från namnet, använder tabelltjänsten tabellformat för att lagra data. Varje rad i tabellen representerar en entitet i standard-terminologi och kolumnerna lagra de olika egenskaperna för denna entitet. Varje entitet har ett par nycklar för att skilja den och en kolumn för tidsstämpling Table service använder för att spåra när entiteten senast uppdaterade (tidsstämpelfältet läggs automatiskt och du kan inte skriva över tidsstämpeln manuellt med ett godtyckligt värde). Table service använder den här senaste ändring tidsstämpeln (LMT) för att hantera Optimistisk samtidighet.  

> [!NOTE]
> Table service REST API-åtgärder kan också returnera en **ETag** värdet som den härleds från tidsstämpel för senaste ändring (LMT). I det här dokumentet ser du villkoren ETag och LMT synonymt eftersom de refererar till samma underliggande data.  
> 
> 

I följande exempel visar en enkel tabelldesign för att lagra anställda och avdelning entiteter. Många av de exempel som visas längre fram i den här handboken är baserade på den här enkla designen.  

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
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Don</td>
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
<th>FirstName</th>
<th>LastName</th>
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
<th>FirstName</th>
<th>LastName</th>
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


Hittills liknar den här designen en tabell i en relationsdatabas med de viktigaste skillnaderna är de obligatoriska kolumnerna och möjlighet att lagra flera typer av enheter i samma tabell. Dessutom var och en av de användardefinierade egenskaperna som **FirstName** eller **ålder** har en datatyp som heltal eller ett strängvärde, precis som en kolumn i en relationsdatabas. Men till skillnad från i en relationsdatabas, Table service utan schema natur innebär att en egenskap inte behöver ha samma datatyp för varje entitet. För att lagra komplexa datatyper i en enskild egenskap, måste du använda ett serialiserade format som JSON eller XML. Läs mer om tabelldatatyper för tjänsten som stöds, stöds datumintervall, namngivningsregler och begränsningar för storlek, [förstå den tabelltjänst-datamodellen](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Eftersom du kommer att se ditt val av **PartitionKey** och **RowKey** är nyckeln till bra tabelldesign. Alla entiteter som lagras i en tabell måste ha en unik kombination av **PartitionKey** och **RowKey**. Precis som med nycklar i en relationsdatabas-tabell, den **PartitionKey** och **RowKey** värden indexeras för att skapa ett grupperat index som möjliggör snabb look-ups; men tabelltjänsten skapar inte någon sekundära index så att de bara två indexerade egenskaper (vissa av de mönster som beskrivs senare visa hur du kan undvika den här tydligt begränsning).  

En tabell består av en eller flera partitioner och att se, många av de designbesluten kommer att finnas kvar att välja en lämplig **PartitionKey** och **RowKey** att optimera din lösning. En lösning kan bestå av bara en enda tabell som innehåller alla dina entiteter som är uppdelad i partitioner, men vanligtvis en lösning har flera tabeller. Tabeller hjälper dig att organisera dina entiteter, hjälper dig att hantera åtkomst till data med hjälp av åtkomstkontrollistor logiskt och du kan släppa en hel tabell med en enda lagringsåtgärd.  

### <a name="table-partitions"></a>Tabellpartitioner
Kontonamn, tabell och **PartitionKey** tillsammans identifierar partitionen i lagringstjänsten där tabelltjänsten sparar entiteten. Förutom att en del av adresseringsschema för entiteter, partitioner för att definiera ett omfång för transaktioner (se [Entitetsgrupptransaktioner](#entity-group-transactions) nedan), och utgör grunden för hur tabelltjänsten skalas. Läs mer på partitioner, [skalbarhet för lagring av Azure- och prestandamål](../storage/common/storage-scalability-targets.md).  

I Table service, en enskild nod tjänst eller mer Slutför partitioner och skalor för tjänsten genom att dynamiskt belastningsutjämning partitionerna mellan noder. Om en nod är under belastning, table service kan *dela* antal partitioner som underhålls av noden till andra noder; när trafik löst, kan tjänsten *merge* partitionsintervall från tyst noder tillbaka till en enda nod.  

Mer information om de interna detaljerna för tabelltjänsten, särskilt hur tjänsten hanterar partitionerna, finns i dokumentet [Microsoft Azure Storage: A med hög Available Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Entitetsgrupptransaktioner
I tabelltjänsten är Entitetsgrupptransaktioner (EGTs) bara inbyggd mekanism för att utföra atomiska uppdateringar i flera entiteter. EGTs också kallas *batch transaktioner* i viss dokumentation. EGTs endast tillämpas på entiteter som lagras i samma partition (dela samma partitionsnyckel i en viss tabell), så när du behöver atomiska transaktionella beteende i flera entiteter som du behöver för att säkerställa att dessa entiteter i samma partition. Det här är ofta en orsak till att hålla flera typer av enheter i samma tabell (och partition) och inte använder flera tabeller för olika enhetstyper. En enda EGT tillämpas på högst 100 entiteter.  Om du skickar in flera samtidiga EGTs för bearbetning, är det viktigt att se till att dessa EGTs inte fungerar på enheter som är vanliga i EGTs som annars bearbetning kan fördröjas.

EGTs medför också en potentiell kompromiss där du kan utvärdera i din design: med hjälp av fler partitioner ökar skalbarheten i ditt program eftersom Azure har fler möjligheter för Utjämning av nätverksbelastning begäranden mellan noder, men detta kan begränsa möjligheten för ditt program att utföra atomiska transaktioner och underhålla stark konsekvens för dina data. Det finns också specifika skalbarhetsmål på nivån för en partition som kan begränsa dataflödet för transaktioner som du kan förvänta dig för en enskild nod: Läs mer om skalbarhetsmål för Azure storage-konton och table service [Azure Storage skalbarhets- och prestandamål](../storage/common/storage-scalability-targets.md). Senare avsnitt i den här guiden beskrivs olika design strategier som hjälper dig att hantera avvägningarna som den här och diskutera hur du bäst för att välja din partitionsnyckel baserat på de specifika kraven för klientprogrammet.  

### <a name="capacity-considerations"></a>Överväganden för kapacitet
Följande tabell innehåller några nyckelvärden känna till när du utformar en lösning för tabellen:  

| Total kapacitet för ett Azure storage-konto | 500 TB |
| --- | --- |
| Antalet tabeller i ett Azure storage-konto |Begränsas bara av kapaciteten för storage-konto |
| Antalet partitioner i en tabell |Begränsas bara av kapaciteten för storage-konto |
| Antal entiteter i en partition |Begränsas bara av kapaciteten för storage-konto |
| Storleken på en enskild entitet |Upp till 1 MB med högst 255 egenskaper (inklusive den **PartitionKey**, **RowKey**, och **tidsstämpel**) |
| Storleken på den **PartitionKey** |En sträng upp till 1 KB stora |
| Storleken på den **RowKey** |En sträng upp till 1 KB stora |
| Storleken på en Entitetsgrupp-transaktion |En transaktion får innehålla högst 100 entiteter och nyttolasten måste vara mindre än 4 MB i storlek. En entitet kan bara uppdatera en gång i en EGT. |

Mer information finns i [Understanding the Table Service Data Model](http://msdn.microsoft.com/library/azure/dd179338.aspx) (Så här fungerar datamodellen för Table Storage).  

### <a name="cost-considerations"></a>Kostnadsöverväganden
Table storage är relativt prisvärda, men du bör innehålla kostnadsuppskattningar för både kapacitetsförbrukning och antalet transaktioner som en del av utvärderingen av alla lösningar som använder Table service. I många scenarier som lagrar Avnormaliserade eller duplicerade data för att förbättra är prestanda och skalbarhet för din lösning dock en giltig metod för att ta. Mer information om priser finns i [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Riktlinjer för tabelldesign
Dessa listor sammanfatta några av de viktiga riktlinjer som du bör tänka på när du utformar dina tabeller och den här guiden att behandla dem alla i detalj senare i. Dessa riktlinjer skiljer sig från riktlinjerna som följer du vanligtvis för relationsdatabasdesignen.  

Utforma din lösning för tabellen vara *läsa* effektiv:

* ***Design för frågor i Läs-aktiverat program.*** När du utformar dina tabeller, tänka frågor (särskilt svarstiden känsliga som) som du ska köra innan du tänka på hur du uppdaterar dina entiteter. Detta innebär vanligtvis en effektiv och högpresterande lösning.  
* ***Ange både PartitionKey och RowKey i dina frågor.*** *Peka frågor* som dessa är de mest effektiva frågorna i table service.  
* ***Överväg att lagra dubbletter av entiteter.*** Table storage är billiga så fundera över lagra samma entitet flera gånger (med olika nycklar) om du vill aktivera mer effektiva frågor.  
* ***Överväg att avnormalisera data.*** Table storage är billiga så fundera över avnormalisera data. Till exempel lagra sammanfattning entiteter så att frågor för aggregerade data bara behöver åtkomst till en enda entitet.  
* ***Använd sammansatt nyckelvärden.*** De enda nycklarna som du har är **PartitionKey** och **RowKey**. Till exempel använda sammansatt nyckelvärden för att aktivera alternativa knappade åtkomstsökvägar entiteter.  
* ***Använd fråga projektion.*** Du kan minska mängden data som du överför över nätverket med hjälp av frågor som väljer enbart de fält som du behöver.  

Utforma din lösning för tabellen vara *skriva* effektiv:  

* ***Skapa inte heta partitioner.*** Välj nycklar som gör det möjligt att sprida dina förfrågningar över flera partitioner vid någon tidpunkt.  
* ***Undvik trafiktoppar.*** Utjämna trafiken över en rimlig tidsperiod och undvika toppar i trafiken.
* ***Inte nödvändigtvis att skapa en separat tabell för varje typ av entiteten.*** När du behöver atomiska transaktioner över entitetstyper kan lagra du dessa flera typer av enheter i samma partition i samma tabell.
* ***Överväg att det maximala dataflöde som du måste uppnå.*** Du måste känna till det för skalbarhetsmål för tabelltjänsten och säkerställa att din design kommer du att överskrida dem.  

När du läser den här handboken visas exempel som placerar alla dessa principer i praktiken.  

## <a name="design-for-querying"></a>Utforma för frågor
Table service-lösningar kan läsas intensiva, Skriv intensiva eller en blandning av båda. Det här avsnittet beskrivs saker att ha i åtanke när du utformar din tabelltjänsten för läsåtgärder effektivt. Normalt är också en design som stöder läsåtgärder effektivt effektivt för skrivåtgärder. Men det finns ytterligare saker att ha i åtanke när du utformar för skrivåtgärder, beskrivs i nästa avsnitt, [Design för dataändringar](#design-for-data-modification).

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

### <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Hur ett PartitionKey och RowKey påverkar prestanda för frågor
I följande exempel förutsätter tabelltjänsten lagrar medarbetare entiteter med följande struktur (de flesta av exemplen utelämna den **tidsstämpel** egenskapen för tydlighetens skull):  

| *Kolumnnamn* | *Datatyp* |
| --- | --- |
| **PartitionKey** (avdelningsnamn) |Sträng |
| **RowKey** (anställnings-Id) |Sträng |
| **Förnamn** |Sträng |
| **Efternamn** |Sträng |
| **Ålder** |Integer |
| **E-postadress** |Sträng |

Det tidigare avsnittet [översikt över Azure Table service](#overview) beskriver några av de viktigaste funktionerna i Azure Table service som har en direkt inverkan på utformning av frågan. Dessa resultera i följande allmänna riktlinjer för att utforma Table service-frågor. Syntaxen för filtret som används i exemplen nedan är från tabelltjänsten REST API för mer information finns i [fråga entiteter](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

* En ***punkt fråga*** är den mest effektiva sökningen att använda och rekommenderas som ska användas för stora volymer sökningar eller sökningar som kräver lägsta svarstid. Sådan fråga kan använda index för att hitta en enskild entitet effektivt genom att ange både den **PartitionKey** och **RowKey** värden. Till exempel: $filter = (PartitionKey eq ”försäljning”) och (RowKey eq ”2”)  
* Andra bäst är en ***intervallet fråga*** som använder den **PartitionKey** och filter på flera olika **RowKey** värden att returnera mer än en entitet. Den **PartitionKey** värdet identifierar en specifik partition och **RowKey** identifierar en delmängd av entiteter i partitionen. Till exempel: $filter = PartitionKey-eq ”försäljning och RowKey ge” och RowKey lt 'T'  
* Tredje bästa är en ***Partition skanna*** som använder den **PartitionKey** och filter på en annan icke-nyckelegenskapen och som kan returnera fler än en entitet. Den **PartitionKey** värdet identifierar en specifik partition och egenskapen värden väljer du för en delmängd av entiteter i partitionen. Till exempel: $filter = PartitionKey eq 'försäljnings- och efternamn eq ”Smith”  
* En ***tabell skanna*** omfattar inte den **PartitionKey** och är ineffektiv eftersom den söker igenom alla partitioner som utgör tabellen i sin tur för alla matchande entiteter. Den utför en tabellgenomsökning oavsett om ditt filter använder den **RowKey**. Till exempel: $filter = LastName eq ”Jones”  
* Frågor som returnerar flera entiteter returnera dem sorterad i **PartitionKey** och **RowKey** ordning. För att undvika att behöva använda entiteter i klienten, Välj en **RowKey** som definierar de vanligaste sorteringsordning.  

Med hjälp av en ”**eller**” att ange ett filter baserat på **RowKey** standardvärden resulterar i en partition genomsökning och inte behandlas som en fråga för intervallet. Därför bör du undvika frågor använder filter som: $filter = PartitionKey eq ”försäljning” och (RowKey eq '121' eller RowKey eq '322 ”)  

Exempel på klientsidan kod som använder Storage-klientbiblioteket för att köra effektiva frågor finns:  

* [Kör en punkt-fråga med hjälp av Storage-klientbibliotek](#executing-a-point-query-using-the-storage-client-library)
* [Hämtar flera entiteter med hjälp av LINQ](#retrieving-multiple-entities-using-linq)
* [Projektion för serversidan](#server-side-projection)  

Exempel på klientsidan kod som kan hantera flera enhetstyper lagras i samma tabell finns:  

* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  

### <a name="choosing-an-appropriate-partitionkey"></a>Välja en lämplig PartitionKey
Ditt val av **PartitionKey** ska stämma överens behovet av att aktivera användning av EGTs (för att säkerställa konsekvens) mot kravet att distribuera dina entiteter över flera partitioner (för att säkerställa en skalbar lösning).  

Vid en extreme du kan lagra alla entiteter i en enda partition, men detta kan begränsa skalbarheten i din lösning och kan förhindra tabelltjänsten från att kunna belastningsutjämna begäranden. Du kan lagra en entitet per partition, vilket är mycket skalbar och som gör det möjligt för tabelltjänsten att belastningsutjämna begäranden, men som skulle hindra dig från att använda entitetsgrupptransaktioner på andra yttersta.  

Perfekt **PartitionKey** är en som gör att du kan använda effektiva frågor och som har tillräckligt med partitioner för att se till att din lösning är skalbar. Du hittar normalt att dina entiteter har en lämplig egenskap som distribuerar dina entiteter i tillräckligt med partitioner.

> [!NOTE]
> I ett system som lagrar information om användare eller anställda, till exempel vara användar-ID en bra PartitionKey. Du kan ha flera entiteter som använder ett visst användar-ID som partitionsnyckel. Varje entitet som lagrar data om en användare är grupperade i en enda partition och så dessa entiteter är tillgängliga via entitetsgrupptransaktioner, samtidigt som det är mycket skalbara.
> 
> 

Det finns fler överväganden i valt **PartitionKey** som relaterar till hur du ska infoga, uppdatera och ta bort entiteter: Se avsnittet [Design för dataändringar](#design-for-data-modification) nedan.  

### <a name="optimizing-queries-for-the-table-service"></a>Optimera frågor för Table service
Table service indexerar automatiskt dina entiteter med den **PartitionKey** och **RowKey** värden i ett enda grupperat index, därför orsaken till att peka frågor är det mest effektiva sättet att använda. Det finns dock några index än som på det grupperade indexet på den **PartitionKey** och **RowKey**.

Många design måste uppfylla kraven för att aktivera sökning efter enheter baserat på flera villkor. Hitta exempelvis anställdas enheter baserat på e-post, anställnings-id eller efternamn. Följande mönster i avsnittet [tabell designmönster](#table-design-patterns) dessa typer av krav och beskriver olika sätt att lösa det faktum att tabelltjänsten inte innehåller sekundära index:  

* [Intra-partition sekundärt index mönstret](#intra-partition-secondary-index-pattern) -Store flera kopior av varje entitet med hjälp av olika **RowKey** värden (i samma partition) för att aktivera snabb och effektiv uppslag och alternativ sorteringsordningar med hjälp av olika **RowKey** värden.  
* [Mönster för kommunikation mellan sekundära Partitionsindex](#inter-partition-secondary-index-pattern) -Store flera kopior av varje entitet med hjälp av olika **RowKey** värden i separata partitioner eller i separata tabeller för att aktivera snabb och effektiv uppslag och alternativ sortera order med hjälp av olika **RowKey** värden.  
* [Index entiteter mönstret](#index-entities-pattern) -Underhåll index entiteter för att aktivera effektiv sökning som returnerar en lista över entiteter.  

### <a name="sorting-data-in-the-table-service"></a>Sortera data i Table service
Table service returnerar entiteter sorterade i stigande ordning baserat på **PartitionKey** och sedan efter **RowKey**. De här nycklarna är strängvärden och för att säkerställa att numeriska värden sorteras korrekt, bör du konvertera dem till en fast längd och fylla dem med nollor. Exempelvis anställdas id-värde som du använder som den **RowKey** är ett heltal, bör du konvertera anställnings-id **123** till **00000123**.  

Många program har kraven för att använda data som sorterats i olika ordning: till exempel sortera anställda efter namn eller genom att gå med datum. Följande mönster i avsnittet [tabell designmönster](#table-design-patterns) åtgärda så alternativ sorteringsordning för dina entiteter:  

* [Intra-partition sekundärt index mönstret](#intra-partition-secondary-index-pattern) – Store flera kopior av varje entitet som använder olika RowKey värden (i samma partition) för att aktivera snabb och effektiv uppslag och alternativ sortera beställningar med hjälp av olika RowKey värden.  
* [Mönster för kommunikation mellan sekundära Partitionsindex](#inter-partition-secondary-index-pattern) – Store flera kopior av varje entitet med hjälp av olika RowKey värden i separata partitioner i separata tabeller vill aktivera snabb och effektiv uppslag och alternativ sortera beställningar med hjälp av olika RowKey värden .
* [Log tail mönstret](#log-tail-pattern) -hämta den *n* entiteter som nyligen lagt till en partition med hjälp av en **RowKey** värde som sorteras listan i omvänd datum- och tidsordning.  

## <a name="design-for-data-modification"></a>Utforma för dataändring
Det här avsnittet fokuserar på designöverväganden för att optimera infogningar, uppdateringar och borttagningar. I vissa fall behöver du utvärdera det är säkerhetsaspekten Designer som optimerar för frågor mot Designer som optimerar för dataändringar precis som i utformning för relationsdatabaser (även om teknikerna för att hantera design avvägningarna är skiljer sig i en relationsdatabas). Avsnittet [tabell designmönster](#table-design-patterns) beskriver vissa detaljerad designmönster för Table service och visar några av de här kompromisser. I praktiken märker du att många Designer som optimerats för att fråga entiteter också fungerar bra för att ändra entiteter.  

### <a name="optimizing-the-performance-of-insert-update-and-delete-operations"></a>Optimera prestanda för insert-, uppdatera och ta bort
Om du vill uppdatera eller ta bort en entitet, måste du att kunna identifiera den med hjälp av den **PartitionKey** och **RowKey** värden. I detta avseende ditt val av **PartitionKey** och **RowKey** ändra entiteter bör följa liknande kriterier för att stödja punktfrågor eftersom du vill identifiera enheter som effektivt som möjligt. Du inte vill använda en ineffektiv skanning för partitionen eller tabell för att hitta en entitet för att identifiera den **PartitionKey** och **RowKey** värden som du behöver uppdatera eller ta bort den.  

Följande mönster i avsnittet [tabell designmönster](#table-design-patterns) åtgärda optimera prestanda eller din infoga, uppdatera och ta bort:  

* [Hög volym ta bort mönstret](#high-volume-delete-pattern) -aktivera borttagningen av ett stort antal entiteter genom att lagra alla entiteter för samtidiga borttagning i sina egna separata tabeller; du ta bort entiteter genom att ta bort tabellen.  
* [Serien datamönster](#data-series-pattern) -Store fullständig dataserier i en enda entitet att minimera antalet begäranden som du gör.  
* [Mönster för många entiteter](#wide-entities-pattern) -använder flera fysiska enheter för att lagra logiska entiteter med mer än 252 egenskaper.  
* [Mönster för stora entiteter](#large-entities-pattern) -använda blob storage för att lagra stora egenskapsvärden.  

### <a name="ensuring-consistency-in-your-stored-entities"></a>Säkerställa konsekvens i dina lagrade entiteter
Den viktiga faktor som påverkar ditt val av nycklar för att optimera dataändringar är att säkerställa konsekvens med hjälp av atomiska transaktioner. Du kan bara använda en EGT för att arbeta med entiteter som lagras i samma partition.  

Följande mönster i avsnittet [tabell designmönster](#table-design-patterns) adress hantera konsekvens:  

* [Intra-partition sekundärt index mönstret](#intra-partition-secondary-index-pattern) -Store flera kopior av varje entitet med hjälp av olika **RowKey** värden (i samma partition) för att aktivera snabb och effektiv uppslag och alternativ sorteringsordningar med hjälp av olika **RowKey** värden.  
* [Mönster för kommunikation mellan sekundära Partitionsindex](#inter-partition-secondary-index-pattern) – Store flera kopior av varje entitet som använder olika RowKey värden i olika partitioner eller separata tabeller för att aktivera snabb och effektiv uppslag och alternativ sortera beställningar med hjälp av olika **RowKey** värden.  
* [Konsekvent transaktioner mönstret](#eventually-consistent-transactions-pattern) -aktivera konsekvent beteenden över partitionsgränser eller gränser för storage-system med hjälp av Azure-köer.
* [Index entiteter mönstret](#index-entities-pattern) -Underhåll index entiteter för att aktivera effektiv sökning som returnerar en lista över entiteter.  
* [Mönster för denormalisering](#denormalization-pattern) -kombinera relaterade data tillsammans i en enda enhet så att du kan hämta alla data som du behöver med en enda fråga.  
* [Serien datamönster](#data-series-pattern) -Store fullständig dataserier i en enda entitet att minimera antalet begäranden som du gör.  

Information om entitetsgrupptransaktioner finns i avsnittet [Entitetsgrupptransaktioner](#entity-group-transactions).  

### <a name="ensuring-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Att se till att din design för effektiv ändringar underlättar effektiva frågor
En design för effektiva frågor ger effektiv ändringar, men du bör alltid i många fall kan utvärdera om så är fallet för ditt specifika scenario. Några av mönster i avsnittet [tabell designmönster](#table-design-patterns) uttryckligen utvärdera avvägningarna mellan fråga och ändra entiteter och du får alltid ha i åtanke antalet varje typ av åtgärd.  

Följande mönster i avsnittet [tabell designmönster](#table-design-patterns) åtgärda avvägningarna mellan designa för effektiva frågor och designar för effektiv dataändringar:  

* [Sammansatt nyckel mönstret](#compound-key-pattern) -Använd sammansatta **RowKey** värden för att aktivera en klient för att söka efter relaterade data med en enda fråga.  
* [Log tail mönstret](#log-tail-pattern) -hämta den *n* entiteter som nyligen lagt till en partition med hjälp av en **RowKey** värde som sorteras listan i omvänd datum- och tidsordning.  

## <a name="encrypting-table-data"></a>Kryptering av tabelldata som
Klientbiblioteket för .NET Azure Storage stöder kryptering av strängen Entitetsegenskaper för insert och ersätt-åtgärder. Krypterade strängar som är lagrade på tjänsten som binära egenskaper och de konverteras till strängar efter dekryptering.    

För tabeller, utöver krypteringsprincipen, måste användare ange egenskaper som ska krypteras. Detta kan göras antingen att ange attributet [EncryptProperty] (för POCO entiteter som härleds från TableEntity) eller en kryptering matcharen i alternativen för certifikatförfrågan. En kryptering Konfliktlösaren är en delegat som tar en partitionsnyckel och radnyckel egenskapsnamn och returnerar ett booleskt värde som anger om egenskapen ska vara krypterad. Under krypteringen använder klientbiblioteket den här informationen för att avgöra om en egenskap ska krypteras vid skrivning till ledningen. Delegaten ger också möjlighet att logic kring hur egenskaper som är krypterade. (Till exempel om X, sedan kryptera egenskapen A; annars kryptera egenskaper A och b) Du behöver inte ange den här informationen vid läsning eller fråga entiteter.

Koppling stöds inte för närvarande. Eftersom en delmängd av egenskaperna kan har krypterats tidigare med hjälp av en annan nyckel leder bara sammanslagning av de nya egenskaperna och uppdatera metadata som till förlust av data. Sammanslagning av antingen kräver extra service anrop till läsa befintliga entiteten från tjänsten, eller med en ny nyckel per egenskap som lämpar sig inte av prestandaskäl.     

Information om hur du krypterar tabelldata finns i [Client Side Encryption och Azure Key Vault för Microsoft Azure Storage](../storage/common/storage-client-side-encryption.md).  

## <a name="modeling-relationships"></a>Modellera relationer
Modellutveckling domän är ett viktigt steg i utformningen av komplexa system. Normalt använder du modelleringsprocessen för att identifiera enheter och relationer mellan dem som ett sätt att förstå företagsdomänen och informera utformningen av systemet. Det här avsnittet beskrivs hur du kan översätta några vanliga relationstyper hittades i domänmodeller till utformning för Table service. Processen för mappningen från en logisk datamodell till en fysisk NoSQL-baserade-datamodell skiljer sig från det som används när du utformar en relationsdatabas. Relationsdatabaser design förutsätter vanligtvis en process för normalisering av data som optimerats för att minimera redundans – och en deklarativ förfrågningar till funktion som tar fram hur implementeringen av hur databasen fungerar.  

### <a name="one-to-many-relationships"></a>En-till-många-relationer
En-till-många-relationer mellan företag domänobjekt inträffar ofta: till exempel en avdelning har många anställda. Det finns flera sätt att implementera en-till-många-relationer i Table service varje med för- och nackdelar som kan vara relevanta för ett visst scenario.  

Studera exemplet i stora multinationella företag med tiotusentals avdelningar och medarbetare entiteter där alla avdelningar har många anställda och varje medarbetare som som är associerade med en viss avdelning. En metod är att lagra separat avdelning och medarbetare entiteter som dessa:  

![Avdelning och employee entitet][1]

Det här exemplet visar en implicit en-till-många-relation mellan de typer som baseras på den **PartitionKey** värde. Varje avdelning kan ha många anställda.  

Det här exemplet visar också en avdelning entitet och entiteter relaterade anställda i samma partition. Du kan välja att använda olika partitioner, tabeller eller även lagringskonton för olika enhetstyper.  

En annan metod är att avnormalisera data och lagra bara medarbetare entiteter med Avnormaliserade avdelningsdata som visas i följande exempel. I det här scenariot kanske med den här Avnormaliserade metoden inte bäst om du har ett krav för att kunna ändra information om en avdelningschef, eftersom om du vill göra detta måste du uppdatera alla medarbetare i avdelningen.  

![][2]

Mer information finns i den [Denormalisering mönstret](#denormalization-pattern) senare i den här guiden.  

I följande tabell sammanfattas- och nackdelar med var och en av de metoder som beskrivs ovan för att lagra anställda och avdelning entiteter som har en en-till-många-relation. Du kan också hur ofta du förväntar dig att utföra olika åtgärder: Det kan vara att ha en design som innehåller en kostsam åtgärd om åtgärden endast inträffar sällan.  

<table>
<tr>
<th>Metoden</th>
<th>Experter</th>
<th>Nackdelar</th>
</tr>
<tr>
<td>Separata entitetstyper, samma partition, samma tabell</td>
<td>
<ul>
<li>Du kan uppdatera en avdelning entitet med en enda åtgärd.</li>
<li>Du kan använda en EGT för att upprätthålla konsekvens om du har ett krav att ändra en avdelning entitet när du uppdatera/Infoga/Ta bort en medarbetare entitet. Exempel: Om du ha ett antal avdelningens anställda för varje avdelning.</li>
</ul>
</td>
<td>
<ul>
<li>Du kan behöva hämta både anställda och en avdelning entitet för vissa aktiviteter för klienten.</li>
<li>Lagringsåtgärder inträffa i samma partition. Vid hög transaktionsvolymer kan detta resultera i en hotspot.</li>
<li>Du kan inte flytta en medarbetare till en ny avdelning med hjälp av en EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Separata entitetstyper, olika partitioner eller tabeller eller storage-konton</td>
<td>
<ul>
<li>Du kan uppdatera en avdelning entitet eller medarbetare enhet med en enda åtgärd.</li>
<li>Vid hög transaktionsvolymer kan detta hjälpa att sprida belastningen över flera partitioner.</li>
</ul>
</td>
<td>
<ul>
<li>Du kan behöva hämta både anställda och en avdelning entitet för vissa aktiviteter för klienten.</li>
<li>Du kan inte använda EGTs för att underhålla konsekvensen när du uppdatera/Infoga/Ta bort en medarbetare och uppdatera en avdelning. Till exempel uppdatering av ett antal anställda i en avdelning entitet.</li>
<li>Du kan inte flytta en medarbetare till en ny avdelning med hjälp av en EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Avnormalisera till samma enhetstyp</td>
<td>
<ul>
<li>Du kan hämta all information du behöver med en enskild begäran.</li>
</ul>
</td>
<td>
<ul>
<li>Det kan vara dyrt att upprätthålla konsekvens om du vill uppdatera avdelningens information (detta skulle kräva att du uppdaterar alla anställda i en avdelning).</li>
</ul>
</td>
</tr>
</table>

Hur du väljer mellan dessa alternativ och vilka av för- och nackdelar som är störst, beror på dina specifika Programscenarier. Till exempel hur ofta du ändrar avdelning entiteter; behöver dina medarbetare frågor avdelningens extrainformationen; hur nära kommer du att skalbarhetsgränserna på din partitioner eller ditt storage-konto?  

### <a name="one-to-one-relationships"></a>Motsvarighet
Domänmodeller kan innehålla 1: 1-relationer mellan entiteter. Om du behöver implementera ett förhållande i Table service, måste du också välja hur du länkar de två relaterade entiteterna när du behöver att hämta dem båda. Den här länken kan vara implicit, baserat på en konvention i nyckelvärdena eller explicit genom att lagra en länk i form av **PartitionKey** och **RowKey** värdena i varje entitet till dess relaterad entitet. En diskussion om huruvida du ska lagra relaterade entiteter i samma partition, finns i avsnittet [en-till-många-relationer](#one-to-many-relationships).  

Det finns också tänka på vid implementering som kan leda dig att implementera motsvarighet i Table service:  

* Hantering av stora entiteter (Mer information finns i [stora entiteter mönstret](#large-entities-pattern)).  
* Implementera åtkomstkontroller (Mer information finns i [Kontrollera åtkomst med signaturer för delad åtkomst](#controlling-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Gå med i klienten
Även om det finns olika sätt att modeller av relationer i Table service, bör du inte glömma att två huvudsakliga skäl till att använda tabelltjänsten finns skalbarhet och prestanda. Om du hittar du modellering många relationer som kan påverka prestanda och skalbarhet för din lösning, bör du be dig själv om det är nödvändigt att skapa alla datarelationer i tabelldesign. Du kan förenkla utformningen och förbättra skalbarhet och prestanda för din lösning om du låter ditt klientprogram som utför alla nödvändiga kopplingar.  

Till exempel om du har liten tabeller som innehåller data som inte ändras ofta, kan sedan du hämta dessa data en gång och cachelagra på klienten. Detta kan undvika upprepade görs för att hämta samma data. I exempel vi har tittat på i den här guiden sannolikt uppsättning avdelningar i en liten organisation att vara liten och ändra sällan att göra det en bra kandidat för data som klientprogram kan ladda ned en gång och cacheminnet som Uppslagsdata.  

### <a name="inheritance-relationships"></a>Arvsrelationer
Om klientprogrammet använder en uppsättning klasser som utgör en del av en arvsrelation att representera affärsentiteter, kan du enkelt kan behålla dessa entiteter i Table service. Du kan till exempel ha följande uppsättning klasser som definieras i ditt klientprogram där **Person** är en abstrakt klass.

![][3]

Du kan även spara instanser av de två konkreta klasserna i Table service med hjälp av en enskild Person-tabell med entiteter i den ser ut så här:  

![][4]

Mer information om hur du arbetar med flera typer av enheter i samma tabell i klientkod finns i avsnittet [erfarenhet av heterogena entitetstyper](#working-with-heterogeneous-entity-types) senare i den här guiden. Detta ger exempel på hur du känner igen entitetstypen i klientkod.  

## <a name="table-design-patterns"></a>Designmönster för tabell
Du har sett detaljerad diskussioner om hur du optimerar dina tabelldesign för både hämta entitetsdata med hjälp av frågor och för att infoga, uppdatera och ta bort entitetsdata i föregående avsnitt. Det här avsnittet beskrivs vissa mönster som är lämplig för användning med lösningar för Table service. Dessutom visas hur du praktiskt taget kan lösa vissa problem och kompromisser har aktiverats tidigare i den här guiden. Följande diagram sammanfattar relationerna mellan de olika mönster:  

![][5]

Mönstret kartan ovan visar relationer mellan (blå) mönster och antimönster (orange) som finns dokumenterade i den här guiden. Det är naturligtvis många andra mönster som är värda att hänsyn tagits till. Till exempel ett av scenarierna för Tabelltjänsten är att använda den [mönster för materialiserad vy](https://msdn.microsoft.com/library/azure/dn589782.aspx) från den [kommandot fråga ansvar uppdelning (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) mönster.  

### <a name="intra-partition-secondary-index-pattern"></a>Mönster för Intra-partition sekundärt index
Store flera kopior av varje entitet med hjälp av olika **RowKey** värden (i samma partition) att aktivera snabb och effektiv sökningar och alternativa sorteringsordningar med hjälp av olika **RowKey** värden. Uppdateringar mellan kopior kan vara konsekvent med EGTs.  

#### <a name="context-and-problem"></a>Kontext och problem
Table service indexerar automatiskt entiteter med hjälp av den **PartitionKey** och **RowKey** värden. På så sätt kan ett klientprogram att hämta en entitet som effektivt med hjälp av dessa värden. Till exempel använder tabellstrukturen visas nedan, ett klientprogram kan använda en punkt-fråga för att hämta en enskild medarbetare entitet med hjälp av id och ett avdelningsnamn (den **PartitionKey** och **RowKey**  värden). En klient kan också hämta entiteter sorterade efter anställnings-id i varje avdelning.

![Medarbetaren entitet][6]

Om du vill ska kunna hitta en anställd entitet som baseras på värdet för en annan egenskap, till exempel e-postadress, måste du använda en mindre effektivt partition genomsökning för att hitta en matchning. Det beror på att table service inte tillhandahåller sekundära index. Dessutom är det inget alternativ för att begära en lista över anställda sorterad i en annan ordning än **RowKey** ordning.  

#### <a name="solution"></a>Lösning
Undvik bristen på sekundära index, kan du lagra flera kopior av varje entitet med varje kopia med ett annat **RowKey** värde. Om du sparar en entitet med strukturer som visas nedan, kan du enkelt hämta anställdas enheter baserat på e-postadress eller medarbetare id. Prefixet värden för den **RowKey**, ”empid_” och ”email_” kan du fråga för en enskild anställd eller en mängd anställda med hjälp av en mängd e-postadresser eller medarbetare-ID: n.  

![][7]

Följande två filtervillkoren (en Leta upp av anställnings-id och en Leta upp av e-postadress) ange både punktfrågor:  

* $filter = (PartitionKey eq ”försäljning”) och (RowKey eq 'empid_000223 ”)  
* $filter = (PartitionKey eq ”försäljning”) och (RowKey eq 'email_jonesj@contoso.com”)  

Om du frågar efter ett intervall med anställdas enheter, du kan ange ett intervall som är sorterad i anställdas id ordning eller ett intervall som är sorterad i e-postadress ordning genom att fråga om entiteter med ett prefix i den **RowKey**.  

* Du hittar alla anställda på försäljningsavdelningen med en anställnings-id i intervallet 000100 000199 användning: $filter = (PartitionKey eq ”försäljning”) och (RowKey ge ”empid_000100”) och (RowKey le 'empid_000199 ”)  
* Du hittar alla anställda på försäljningsavdelningen med en e-postadress som börjar med bokstaven ”a” Använd: $filter = (PartitionKey eq ”försäljning”) och (RowKey ge ”email_a”) och (RowKey ll ”email_b”)  
  
  Syntaxen för filtret som används i exemplen ovan är från tabelltjänsten REST API för mer information finns i [fråga entiteter](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Table storage är relativt billiga att använda kostnaden arbetet med att lagra duplicerade data bör inte vara en stor utmaning. Du bör dock alltid utvärdera kostnaden för din design utifrån din förväntade lagringsbehov och bara lägga till dubbla entiteter för att stödja de frågor som ska köra klientprogrammet.  
* Eftersom sekundära index entiteterna lagras i samma partition som de ursprungliga entiteterna, bör du kontrollera att du inte överskrider det för skalbarhetsmål för en enskild partition.  
* Du kan behålla dina dubbla entiteter överensstämmer med varandra med hjälp av EGTs för att uppdatera två kopior av entiteten atomiskt. Detta innebär att du ska lagra alla kopior av en entitet i samma partition. Mer information finns i avsnittet [med hjälp av Entitetsgrupptransaktioner](#entity-group-transactions).  
* Det värde som används för den **RowKey** måste vara unikt för varje entitet. Överväg att använda sammansatt nyckelvärden.  
* Utfyllnad numeriska värden i den **RowKey** (till exempel, anställnings-id 000223) kan korrigera sortering och filtrering baserat på övre och nedre gränser.  
* Du behöver inte nödvändigtvis att duplicera alla egenskaperna för entiteten. Exempel: om förfrågningar som söker efter de entiteter som e-postadress-adressen i den **RowKey** behöver aldrig medarbetarens ålder, dessa entiteter kan ha följande struktur:

![][8]

* Det är vanligtvis bättre att lagra duplicerade data och se till att du kan hämta alla data som du behöver med en enda fråga, än att använda en fråga för att hitta en entitet och en annan för att söka efter data som krävs.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när klientprogrammet måste hämta entiteter med hjälp av en mängd olika nycklar när din klient behöver hämta entiteter i olika sorteringsordningar, och där du kan identifiera varje entitet med hjälp av en rad unika värden. Dock bör du se till att du inte överskrider skalbarhetsgränserna för partition när du utför entitet sökningar med hjälp av de olika **RowKey** värden.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Mönster för kommunikation mellan sekundära Partitionsindex](#inter-partition-secondary-index-pattern)
* [Sammansatt nyckel mönster](#compound-key-pattern)
* [Entitetsgrupptransaktioner](#entity-group-transactions)
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Mönster för kommunikation mellan sekundära Partitionsindex
Store flera kopior av varje entitet med hjälp av olika **RowKey** värden i separata partitioner eller i separata tabeller för att aktivera snabb och effektiv uppslag och alternativ sorteringsordningar med hjälp av olika **RowKey**värden.  

#### <a name="context-and-problem"></a>Kontext och problem
Table service indexerar automatiskt entiteter med hjälp av den **PartitionKey** och **RowKey** värden. På så sätt kan ett klientprogram att hämta en entitet som effektivt med hjälp av dessa värden. Till exempel använder tabellstrukturen visas nedan, ett klientprogram kan använda en punkt-fråga för att hämta en enskild medarbetare entitet med hjälp av id och ett avdelningsnamn (den **PartitionKey** och **RowKey**  värden). En klient kan också hämta entiteter sorterade efter anställnings-id i varje avdelning.  

![][9]

Om du vill ska kunna hitta en anställd entitet som baseras på värdet för en annan egenskap, till exempel e-postadress, måste du använda en mindre effektivt partition genomsökning för att hitta en matchning. Det beror på att table service inte tillhandahåller sekundära index. Dessutom är det inget alternativ för att begära en lista över anställda sorterad i en annan ordning än **RowKey** ordning.  

Du att en stor mängd transaktioner mot dessa entiteter och vill du minimera risken för den tabell service hastighet som begränsar din klient.  

#### <a name="solution"></a>Lösning
Undvik bristen på sekundära index, kan du lagra flera kopior av varje entitet med varje kopia med hjälp av olika **PartitionKey** och **RowKey** värden. Om du sparar en entitet med strukturer som visas nedan, kan du enkelt hämta anställdas enheter baserat på e-postadress eller medarbetare id. Prefixet värden för den **PartitionKey**, ”empid_” och ”email_” kan du identifiera det index som du vill använda för en fråga.  

![][10]

Följande två filtervillkoren (en Leta upp av anställnings-id och en Leta upp av e-postadress) ange både punktfrågor:  

* $filter = (PartitionKey eq ' empid_Sales ”) och (RowKey eq '000223”)
* $filter = (PartitionKey eq ' email_Sales ”) och (RowKey eq 'jonesj@contoso.com”)  

Om du frågar efter ett intervall med anställdas enheter, du kan ange ett intervall som är sorterad i anställdas id ordning eller ett intervall som är sorterad i e-postadress ordning genom att fråga om entiteter med ett prefix i den **RowKey**.  

* Du hittar alla anställda på försäljningsavdelningen med en anställnings-id i intervallet **000100** till **000199** sorterade medarbetare id kunna använda: $filter = (PartitionKey eq ' empid_Sales ”) och (RowKey ge '000100') och (RowKey le '000199 ”)  
* Du hittar alla anställda på försäljningsavdelningen med en e-postadress som börjar med ”a” i e-postadress kunna använda: $filter = (PartitionKey eq ' email_Sales ”) och (RowKey ge” a ”) och (RowKey ll” b ”)  

Observera att syntaxen för filtret som används i exemplen ovan är från tabelltjänsten REST API för mer information finns i [fråga entiteter](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du kan behålla dina dubbla entiteter konsekvent med varandra med hjälp av den [konsekvent transaktioner mönstret](#eventually-consistent-transactions-pattern) att underhålla de primära och sekundära index entiteterna.  
* Table storage är relativt billiga att använda kostnaden arbetet med att lagra duplicerade data bör inte vara en stor utmaning. Du bör dock alltid utvärdera kostnaden för din design utifrån din förväntade lagringsbehov och bara lägga till dubbla entiteter för att stödja de frågor som ska köra klientprogrammet.  
* Det värde som används för den **RowKey** måste vara unikt för varje entitet. Överväg att använda sammansatt nyckelvärden.  
* Utfyllnad numeriska värden i den **RowKey** (till exempel, anställnings-id 000223) kan korrigera sortering och filtrering baserat på övre och nedre gränser.  
* Du behöver inte nödvändigtvis att duplicera alla egenskaperna för entiteten. Till exempel om frågorna som lookup entiteter med hjälp av e-postmeddelandet adressen i den **RowKey** behöver aldrig medarbetarens ålder, dessa entiteter kan ha följande struktur:
  
  ![][11]
* Det är vanligtvis bättre att lagra duplicerade data och se till att du kan hämta alla data som du behöver med en enda fråga att använda en fråga för att hitta en entitet med hjälp av det sekundära indexet och en annan för sökning nödvändiga data i det primära indexet än.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när klientprogrammet måste hämta entiteter med hjälp av en mängd olika nycklar när din klient behöver hämta entiteter i olika sorteringsordningar, och där du kan identifiera varje entitet med hjälp av en rad unika värden. Använd det här mönstret när du vill undvika att du överskrider skalbarhetsgränserna för partition när du utför entitet sökningar med hjälp av de olika **RowKey** värden.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Konsekvent transaktioner mönster](#eventually-consistent-transactions-pattern)  
* [Mönster för Intra-partition sekundärt index](#intra-partition-secondary-index-pattern)  
* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Entitetsgrupptransaktioner](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Konsekvent transaktioner mönster
Aktivera konsekvent beteenden över partitionsgränser eller gränser för storage-system med hjälp av Azure-köer.  

#### <a name="context-and-problem"></a>Kontext och problem
EGTs aktivera atomiska transaktioner över flera enheter som delar samma partitionsnyckel. För bättre prestanda och skalbarhet, kanske du vill lagra entiteter som har krav på datakonsekvens i separata partitioner eller i ett separat lagringssystem: i ett sådant scenario, du kan inte använda EGTs för att upprätthålla konsekvens. Du kan till exempel ha ett krav att upprätthålla konsekvens mellan:  

* Entiteter som lagras i två olika partitioner i samma tabell, i olika tabeller eller i olika lagringskonton.  
* En entitet som lagras i tabelltjänsten och en blob som lagras i Blob-tjänsten.  
* En entitet som lagras i tabelltjänsten och en fil i ett filsystem.  
* En entitet store i Table service indexerats ännu med hjälp av Azure Search-tjänsten.  

#### <a name="solution"></a>Lösning
Du kan implementera en lösning som ger konsekvens mellan två eller flera partitioner eller lagringssystem med hjälp av Azure-köer.
Anta att du har ett krav för att kunna arkivera gamla anställdas enheter för att visa den här metoden kan. Den gamla anställdas enheter efterfrågas sällan och bör undantas från alla aktiviteter som handlar om aktuella anställda. För att implementera det här kravet du lagrar aktiva medarbetare i den **aktuella** tabell och tidigare anställda i den **Arkiv** tabell. Arkivera en anställd måste du ta bort enheten från den **aktuella** tabellen och Lägg till entitet till den **Arkiv** tabell, men du kan inte använda en EGT för att utföra de här två åtgärderna. För att undvika risken för att ett fel gör en entitet som ska visas i båda eller ingen tabeller, måste arkivåtgärden vara konsekvent. Följande sekvensdiagram illustrerar stegen i den här åtgärden. Mer information ges för undantag sökvägar i följande text.  

![][12]

En klient initierar Arkiv igen genom att placera ett meddelande på en Azure-kö, i det här exemplet att arkivera medarbetare #456. En arbetsroll söker i kön för nya meddelanden. När den hittar en läser meddelandet och lämnar en dold kopia för kön. Arbetsrollen bredvid hämtar en kopia av entiteten från den **aktuella** tabellen, infogar en kopia i den **Arkiv** tabellen och tar sedan bort ursprungligt från den **aktuella** tabell. Slutligen, om det finns några fel från föregående steg, arbetsrollen tar bort dolda meddelandet från kön.  

I det här exemplet steg 4 infogar medarbetaren till den **Arkiv** tabell. Det kan lägga till medarbetaren till en blob i Blob-tjänsten eller en fil i ett filsystem.  

#### <a name="recovering-from-failures"></a>Återställa från fel
Det är viktigt som åtgärder i steg **4** och **5** måste vara *idempotenta* ifall arbetsrollen behöver starta om arkivåtgärden. Om du använder tabelltjänsten för steget **4** bör du använda en ”infoga eller ersätta” åtgärd; för steg **5** bör du använda en ”ta bort om det finns” igen om du använder klientbiblioteket. Om du använder en annan lagringssystemet, måste du använda en lämplig idempotent åtgärd.  

Om arbetsrollen slutförs aldrig steg **6**, och sedan efter en tidsgräns som meddelandet visas igen för kön som är redo för worker-roll att bearbeta den på nytt. Worker-roll kan kontrollera hur många gånger ett meddelande i kön har läsa och, om det behövs, Flagga det är en ”skadligt” meddelande för undersökning genom att skicka den till en separat kö. Läs mer om läsning av Kömeddelanden och kontrollera antalet ta [hämta meddelanden](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Fel från tabell och kö tjänsterna tillfälliga fel och klientprogrammet bör vara lämplig omprövningslogiken för att hantera dem.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Den här lösningen ger inte för transaktionsisoleringen. Till exempel en klient kan läsa den **aktuella** och **Arkiv** tabeller när arbetsrollen har mellan stegen **4** och **5**, och se en Inkonsekvent vy över data. Data kommer att vara konsekvent så småningom.  
* Du måste vara säker på att steg 4 och 5 är idempotenta för att säkerställa konsekvens.  
* Du kan skala lösningen med hjälp av flera köer och worker-rollinstanser.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill att garantera konsekvens mellan entiteter som finns i olika partitioner eller tabeller. Du kan utöka det här mönstret för att säkerställa konsekvens för åtgärder i Table service och Blob-tjänsten och andra Azure Storage datakällor, till exempel databasen eller filsystemet.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Entitetsgrupptransaktioner](#entity-group-transactions)  
* [Sammanfoga eller ersätta](#merge-or-replace)  

> [!NOTE]
> Om transaktionsisoleringen är viktigt att din lösning, bör du göra om tabellerna så att du kan använda EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Mönster för index-entiteter
Underhålla index entiteter för att aktivera effektiv sökning som returnerar en lista över entiteter.  

#### <a name="context-and-problem"></a>Kontext och problem
Table service indexerar automatiskt entiteter med hjälp av den **PartitionKey** och **RowKey** värden. På så sätt kan ett klientprogram att hämta en entitet som effektivt med en punkt-fråga. Till exempel använder tabellstrukturen visas nedan, ett klientprogram enkelt kan hämta en enskild medarbetare entitet med hjälp av id och ett avdelningsnamn (den **PartitionKey** och **RowKey**).  

![][13]

Om du vill kunna hämta en lista över anställda entiteter baserat på värdet för en annan icke-unikt egenskap, till exempel efternamn, måste du använda en mindre effektivt partition-sökning för att hitta matchningar i stället för att använda ett index för att leta upp dem direkt. Det beror på att table service inte tillhandahåller sekundära index.  

#### <a name="solution"></a>Lösning
Om du vill aktivera sökning efter efternamn med entitetsstruktur som anges ovan, måste du ha en lista över anställda ID: n. Om du vill hämta medarbetare entiteter med ett visst efternamn, till exempel Jones, måste du först lokalisera listan över anställdas ID: n för anställda med Jones som efternamn och hämtar sedan dessa medarbetare entiteter. Det finns tre huvudsakliga alternativ för att lagra listor med medarbetare-ID: n:  

* Använda blob storage.  
* Skapa index entiteter i samma partition som anställdas enheter.  
* Skapa index entiteter i en separat partition eller tabellen.  

<u>Alternativ #1: Använda blob storage</u>  

För det första alternativet, du skapa en blob för varje unikt efternamn och varje blob store en lista över de **PartitionKey** (avdelning) och **RowKey** (anställnings-id) värden för anställda som har det senaste namnet. När du lägger till eller ta bort en medarbetare, bör du kontrollera att innehållet i den relevanta blobben är konsekvent med enheterna som anställda.  

<u>Alternativ #2:</u> skapa index entiteter i samma partition  

För det andra alternativet, använder du index entiteter som lagrar följande data:  

![][14]

Den **EmployeeIDs** egenskapen innehåller en lista över anställda ID: n för anställda med efternamn som lagras i den **RowKey**.  

Följande steg beskriver hur du bör följa när du lägger till en ny medarbetare om du använder det andra alternativet. I det här exemplet lägger vi till en medarbetare med Id 000152 och efternamn Jones på försäljningsavdelningen:  

1. Hämta entiteten index med en **PartitionKey** värdet ”Försäljning” och **RowKey** värdet ”Jones”. Spara ETag för den här entiteten som ska användas i steg 2.  
2. Skapa entiteten grupp transaktion (det vill säga en batchåtgärd) som infogar entiteten medarbetare (**PartitionKey** värdet ”Försäljning” och **RowKey** värdet ”000152”), och uppdaterar indexet entiteten (**PartitionKey** värdet ”Försäljning” och **RowKey** värdet ”Jones”) genom att lägga till nya anställnings-id i listan i fältet EmployeeIDs. Läs mer om entitetsgrupptransaktioner [Entitetsgrupptransaktioner](#entity-group-transactions).  
3. Om entitet grupp transaktionen misslyckas på grund av en Optimistisk samtidighet-fel (någon annan har ändrat entiteten index), måste du börja om igen i steg 1.  

Du kan använda en liknande metod för att ta bort en medarbetare om du använder det andra alternativet. Ändra en anställds efternamn är lite mer komplext eftersom du behöver att köra en entitet grupp transaktion som uppdaterar tre entiteter: anställd entiteten och entiteten index för det gamla efternamnet entiteten index för det nya efternamnet. Varje entitet måste du hämta innan du gör några ändringar för att hämta de ETag-värden som du sedan kan använda för att utföra uppdateringarna med Optimistisk samtidighet.  

Följande steg beskriver hur du bör följa när du behöver leta upp alla anställda med ett visst efternamn på en avdelning om du använder det andra alternativet. Det här exemplet letar vi upp alla anställda med efternamn Jones på försäljningsavdelningen:  

1. Hämta entiteten index med en **PartitionKey** värdet ”Försäljning” och **RowKey** värdet ”Jones”.  
2. Parsa listan över anställnings-ID i fältet EmployeeIDs.  
3. Om du behöver ytterligare information om var och en av dessa anställda (till exempel sina e-postadresser) kan du hämta var och en av anställdas entiteter med hjälp av **PartitionKey** värdet ”Försäljning” och **RowKey** värden från den lista över anställda som du hämtade i steg 2.  

<u>Alternativ #3:</u> skapa index entiteter i en separat partition eller tabell  

Det tredje alternativet Använd index entiteter som lagrar följande data:  

![][15]

Den **EmployeeIDs** egenskapen innehåller en lista över anställda ID: n för anställda med efternamn som lagras i den **RowKey**.  

Du kan inte använda EGTs med det tredje alternativet för att underhålla konsekvensen eftersom indexet entiteter finns i en separat partition från anställdas enheter. Du bör se till att index entiteter är konsekvent med enheterna som anställda.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Denna lösning kräver minst två frågor för att hämta matchande entiteter: en för att fråga indexet entiteter för att hämta listan över **RowKey** värden och frågor för att hämta varje entitet i listan.  
* Tanke på att en enskild entitet har en maximal storlek på 1 MB och alternativ #2 alternativet förutsätter #3 i lösningen att listan över anställdas ID: n för alla angivna efternamn aldrig är större än 1 MB. Om listan över anställdas ID: n är troligt att vara större än 1 MB i storlek, Använd alternativ #1 och lagra indexet data i blob storage.  
* Om du använder alternativet #2 måste (med EGTs som hanterar att lägga till och ta bort anställda och ändra en anställds efternamn) du utvärdera om mängden transaktioner kommer närma dig skalbarhetsgränserna i en given partition. Om så är fallet bör du överväga en konsekvent lösning (alternativ #1 eller #3) som använder köer för att hantera uppdateringsbegäranden och gör det möjligt att lagra dina index entiteter i en separat partition från medarbetare entiteter.  
* Alternativ #2 i den här lösningen förutsätter att du vill leta upp efter efternamn inom en avdelning: till exempel du vill hämta en lista över anställda med ett efternamn Jones på försäljningsavdelningen. Om du vill kunna söka efter alla anställda med ett efternamn Jones i hela organisationen använda antingen alternativ #1 eller #3.
* Du kan implementera en Köbaserad lösning som ger slutlig konsekvens (se den [konsekvent transaktioner mönstret](#eventually-consistent-transactions-pattern) för mer information).  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill söka efter en uppsättning entiteter som alla har en gemensam egenskapsvärdet, till exempel alla anställda med efternamn Jones.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Konsekvent transaktioner mönster](#eventually-consistent-transactions-pattern)  
* [Entitetsgrupptransaktioner](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Denormalisering mönster
Kombinera relaterade data tillsammans i en enda enhet så att du kan hämta alla data som du behöver med en enda fråga.  

#### <a name="context-and-problem"></a>Kontext och problem
I en relationsdatabas normalisera du normalt data för att ta bort duplicering, vilket resulterar i frågor som hämtar data från flera tabeller. Om du normalisera data i Azure-tabeller, måste du se flera kommunikationsturer från klienten till servern för att hämta relaterade data. Till exempel med tabellstrukturen nedan om du behöver två tur och RETUR att hämta information för en avdelning: en för att hämta entiteten avdelning som innehåller den hanterar-id och sedan en annan begäran att hämta chefens information i en anställd entitet.  

![][16]

#### <a name="solution"></a>Lösning
I stället för att lagra data i två separata entiteter, avnormalisera data och behålla en kopia av chefens information i entiteten avdelning. Exempel:  

![][17]

Avdelning entiteter som lagras med de här egenskaperna, kan du nu hämta all information du behöver om en avdelning med hjälp av en punkt-fråga.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Det finns en kostnad som associeras med att lagra vissa data två gånger. Prestanda-förmån (som färre förfrågningar till lagringstjänsten) vanligtvis uppväger den marginell ökade kostnader för lagring (och den här kostnaden är delvis förskjutning genom en minskning av antalet transaktioner som du behöver för att hämta information om en avdelning ).  
* Du måste ha två entiteter som lagrar information om hanterare av konsekvens. Du kan hantera konsekvens problemet med hjälp av EGTs för att uppdatera flera entiteter i en enda atomisk transaktion: i det här fallet avdelning entiteten och entiteten medarbetare för avdelning manager lagras i samma partition.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du ofta behöva leta upp relaterad information. Det här mönstret minskar antalet frågor som din klient måste göra för att hämta de data som krävs.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Entitetsgrupptransaktioner](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Sammansatt nyckel mönster
Använd sammansatta **RowKey** värden att aktivera en klient att söka efter relaterade data med en enda fråga.  

#### <a name="context-and-problem"></a>Kontext och problem
I en relationsdatabas är det naturligt att använda kopplingar i frågor för att gå tillbaka relaterade typer av data till klienten i en enskild fråga. Du kan exempelvis använda anställnings-id för att leta upp en lista över relaterade entiteter som innehåller prestanda och granska data för denna medarbetare.  

Anta att du lagrar medarbetare entiteter i tabelltjänsten med följande struktur:  

![][18]

Du måste också att lagra historiska data som rör granskningar och prestanda för varje år medarbetaren har arbetat för din organisation och du behöver för att kunna komma åt informationen per år. Ett alternativ är att skapa en annan tabell som lagrar entiteter med följande struktur:  

![][19]

Observera att med den här metoden kan du välja att duplicera viss information (till exempel förnamn och efternamn) i den nya entiteten så att du kan hämta dina data med en enskild begäran. Du kan dock ha stark konsekvens eftersom du inte kan använda en EGT för att uppdatera de två entiteterna atomiskt.  

#### <a name="solution"></a>Lösning
Store en ny entitet skapas i den ursprungliga tabellen med entiteter med följande struktur:  

![][20]

Observera hur **RowKey** är nu en sammansatt nyckel som består av anställnings-id och året för de data som gör att du kan hämta medarbetarens prestanda och granska data med en enskild begäran för en enda entitet.  

I följande exempel beskrivs hur du kan hämta alla granska data för en viss medarbetare (till exempel medarbetare 000123 på försäljningsavdelningen):  

$filter = (PartitionKey eq ”försäljning”) och (RowKey ge ”empid_000123”) och (RowKey lt 'empid_000124') & $select = RowKey, Arbetsledarens, Peer omdöme, kommentarer  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du bör använda en lämplig avgränsningstecken som gör det enkelt att parsa den **RowKey** värde: till exempel **000123_2012**.  
* Den här entiteten lagras också i samma partition som andra entiteter som innehåller relaterade data för samma medarbetare, vilket innebär att du kan använda EGTs för att upprätthålla stark konsekvens.
* Du bör överväga hur ofta du frågar data för att fastställa om det här mönstret är lämpligt.  Till exempel om du kommer åt granska data används sällan och huvudsakliga medarbetardata ofta bör du behålla dem som separata entiteter.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra en eller flera relaterade entiteter som du frågar ofta.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Entitetsgrupptransaktioner](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  
* [Konsekvent transaktioner mönster](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Mönster för log tail
Hämta den *n* entiteter som nyligen lagt till en partition med hjälp av en **RowKey** värde som sorteras listan i omvänd datum- och tidsordning.  

#### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt krav är att kunna hämta de senast skapade entiteterna, till exempel de senaste tio utgifter ansökningar som görs av en medarbetare. Tabellen frågar stöd för en **$top** fråga åtgärden att returnera först *n* entiteter från en uppsättning: det finns inga motsvarande frågeåtgärden att returnera de sista n entiteterna i en uppsättning.  

#### <a name="solution"></a>Lösning
Store entiteter med hjälp av en **RowKey** att naturligt sorterar i tidsvärdet i omvänd ordning med hjälp av så den senaste posten är alltid den första i tabellen.  

Du kan till exempel använda en omvänd skalstreck-värde som härletts från aktuellt datum och tid för att kunna hämta de tio senaste utgiftsanspråk som skickats av en medarbetare. I följande C#-kodexempel visas ett sätt att skapa ett lämpligt ”inverterad ticken” värde för en **RowKey** som sorterar från den senaste till äldsta:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Du kan få tillbaka för datum-tid-värden med följande kod:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Tabell-fråga som ser ut så här:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du måste fylla omvänd skalstreck värdet med inledande nollor så strängvärdet sorterar som förväntat.  
* Du måste känna till det för skalbarhetsmål på nivån för en partition. Tänk på att inte skapa hotspot-partitioner.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver att få åtkomst till entiteter i omvänd datum/tid-ordning eller när du behöver åtkomst till de senast tillagda entiteterna.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Lägg till åtkomstgruppen / lägga till ett mönster](#prepend-append-anti-pattern)  
* [Hämtar entiteter](#retrieving-entities)  

### <a name="high-volume-delete-pattern"></a>Mönster för hög volym delete
Aktivera borttagning av ett stort antal entiteter genom att lagra alla entiteter för samtidiga borttagning i sina egna separata tabell. Du kan ta bort entiteter genom att ta bort tabellen.  

#### <a name="context-and-problem"></a>Kontext och problem
Många program ta bort gamla data som behöver inte längre ska vara tillgängliga för ett klientprogram eller som programmet har arkiverats till ett annat lagringsmedium. Du vanligtvis identifiera sådana data genom att ett datum: exempel: du har ett krav att ta bort poster för alla inloggningsförfrågningar som är mer än 60 dagar.  

Ett möjligt design är att använda datum och tid för begäran logga in i den **RowKey**:  

![][21]

Den här metoden undviker partition hotspots eftersom programmet kan infoga och ta bort inloggningen entiteter för varje användare i en separat partition. Den här metoden kan dock vara kostsamt och tidskrävande om du har ett stort antal entiteter eftersom först måste du utföra en tabellgenomsökning för att identifiera alla enheter ska ta bort och sedan måste du ta bort varje gamla entitet. Du kan minska antalet sändningar till servern som krävs för att ta bort de gamla enheterna med batchbearbetning flera delete-begäranden till EGTs.  

#### <a name="solution"></a>Lösning
Använd en separat tabell för varje försök att logga in. Du kan använda entiteten designen ovan för att undvika anslutningar när du infogar entiteter och tar bort gamla enheter är nu helt enkelt en fråga av att ta bort en tabell varje dag (en enda lagringsåtgärd) i stället för att söka efter och tar bort hundratals och tusentals person loggar in entiteter varje dag.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Din design som har stöd för andra sätt som programmet ska använda data, till exempel söka efter specifika entiteter, länkar till andra data eller generering samlar in information?  
* Din design undviker aktiva punkter när du infogar nya entiteter?  
* Förvänta dig en fördröjning om du vill återanvända samma namn när du tar bort den. Är det bättre att alltid använda unikt tabellnamn.  
* Förvänta dig några hastighetsbegränsning när du börjar använda en ny tabell medan tabelltjänsten lär sig mönster i databasåtkomst och distribuerar partitionerna mellan noder. Du bör överväga hur ofta du behöver skapa nya tabeller.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du har ett stort antal entiteter som du måste ta bort samtidigt.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Entitetsgrupptransaktioner](#entity-group-transactions)
* [Ändra entiteter](#modifying-entities)  

### <a name="data-series-pattern"></a>Serien datamönster
Store fullständig dataserier i en enda entitet att minimera antalet begäranden som du gör.  

#### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt scenario är för ett program för att lagra en serie med data som vanligtvis krävs för att hämta allt samtidigt. Ditt program kan till exempel registrera hur många IM meddelanden varje medarbetare skickar varje timme och sedan använda informationen för att rita ut hur många meddelanden varje användare som skickas över föregående 24 timmar. En design kan vara att lagra 24 entiteter för varje medarbetare:  

![][22]

Med den här designen kan du enkelt hitta och uppdatera enheten att uppdatera för varje medarbetare när programmet måste uppdatera värdet för antal meddelande. Men om du vill hämta information för att rita ett diagram för aktiviteten i föregående 24 timmar, måste du hämta 24 entiteter.  

#### <a name="solution"></a>Lösning
Använd följande design med en separat egenskap för att lagra meddelandeantalet för varje timme:  

![][23]

Du kan använda en sammanfogning med den här designen för att uppdatera meddelandeantalet för en anställd för en viss timme. Du kan nu hämta all information du behöver att rita diagram med hjälp av en begäran för en enda entitet.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Om din fullständiga dataserien trädnamnet inte passar i en enda entitet (en entitet kan ha upp till 252 egenskaper), kan du använda ett annat datalager, till exempel en blob.  
* Om du har flera klienter som uppdaterar en entitet samtidigt, kommer du behöva använda den **ETag** att implementera Optimistisk samtidighet. Om du har många klienter kan få du hög konkurrens.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill uppdatera och hämta en serie som är associerade med en enskild entitet.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Mönster för stora entiteter](#large-entities-pattern)  
* [Sammanfoga eller ersätta](#merge-or-replace)  
* [Konsekvent transaktioner mönstret](#eventually-consistent-transactions-pattern) (om du lagrar dataserien i en blob)  

### <a name="wide-entities-pattern"></a>Mönster för många entiteter
Använda flera fysiska enheter för att lagra logiska entiteter med mer än 252 egenskaper.  

#### <a name="context-and-problem"></a>Kontext och problem
En enskild entitet kan ha högst 252 egenskaper (förutom de obligatoriska systemegenskaperna) och kan inte lagra mer än 1 MB data totalt. I en relationsdatabas skulle du vanligtvis få avrunda några gränser för storleken på en rad genom att lägga till en ny tabell och tillämpa en 1-till-1-relation mellan dem.  

#### <a name="solution"></a>Lösning
Du kan lagra flera entiteter för att representera ett objekt för stora företag med mer än 252 egenskaper med Table service. Du kan till exempel använda följande designen som använder två entiteter med olika scheman om du vill spara en uppräkning av antalet IM-meddelanden som skickas av varje medarbetare för de senaste 365 dagarna:  

![][24]

Om du behöver göra en ändring som behöver uppdateras både entiteter för att de alltid är synkroniserade med varandra kan använda du en EGT. Annars kan använda du en enda merge-operation för att uppdatera meddelandeantalet för en viss dag. För att hämta alla data för en enskild anställd måste du hämta båda enheter som du kan göra med två effektiv förfrågningar som använder både en **PartitionKey** och en **RowKey** värde.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Hämta en fullständig logisk entitet innebär att minst två lagringstransaktioner: en för att hämta varje fysisk entitet.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra entiteter vars storlek eller antalet egenskaper överskrider gränserna för en enskild entitet i Table service.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Entitetsgrupptransaktioner](#entity-group-transactions)
* [Sammanfoga eller ersätta](#merge-or-replace)

### <a name="large-entities-pattern"></a>Mönster för stora entiteter
Använda blob storage för att lagra stora egenskapsvärden.  

#### <a name="context-and-problem"></a>Kontext och problem
En enskild entitet kan inte lagra mer än 1 MB data totalt. Om en eller flera av dina egenskaper lagra värden som orsakar den totala storleken på din entitet överskrider värdet, kan inte du lagra hela entiteten i Table service.  

#### <a name="solution"></a>Lösning
Om din entitet överskrider 1 MB i storlek eftersom en eller flera egenskaper innehåller en stor mängd data kan du lagra data i Blob-tjänsten och sedan lagra den blob-adressen i en egenskap i entiteten. Du kan till exempel lagra foto av en medarbetare i blob storage och lagra en länk till bilden i den **foto** egenskap för dina medarbetare entitet:  

![][25]

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* För att upprätthålla konsekvens mellan i Table service och data i Blob-tjänsten använder den [konsekvent transaktioner mönstret](#eventually-consistent-transactions-pattern) att underhålla dina entiteter.
* Hämta en fullständig entitet innebär att minst två lagringstransaktioner: en för att hämta entiteten och en för att hämta blob-data.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill lagra entiteter vars storlek överskrider gränserna för en enskild entitet i Table service.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Konsekvent transaktioner mönster](#eventually-consistent-transactions-pattern)  
* [Mönster för många entiteter](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Lägg till åtkomstgruppen/lägga till ett mönster
Öka skalbarheten när du har ett stort antal infogningar genom att sprida tillägg över flera partitioner.  

#### <a name="context-and-problem"></a>Kontext och problem
Prepending eller lägger till entiteter i dina entiteter för lagrade vanligtvis resulterar i programmet att lägga till nya entiteter i den första eller sista partitionen i en sekvens av partitioner. I det här fallet alla infogningar vid en given tidpunkt som äger rum i samma partition, skapar en hotspot som förhindrar att table service-belastningsutjämning infogar över flera noder och vilket kan orsaka att ditt program att träffa skalbarhetsmål för partition. Till exempel om du har ett program som loggar nätverks- och komma åt genom att anställda kan sedan en entitetsstruktur enligt nedan kan resultera i den aktuella timman partition blir en överbelastad punkt om mängden transaktioner når skalbarhetsmålen för en enskild partition:  

![][26]

#### <a name="solution"></a>Lösning
Följande alternativ entitetsstruktur undviker en hotspot på en viss partition som program loggar händelser:  

![][27]

Meddelande med det här exemplet hur både den **PartitionKey** och **RowKey** är sammansatta nycklar. Den **PartitionKey** använder både avdelning och employee id för att distribuera loggning över flera partitioner.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Stöder den alternativa nyckelstruktur som undviker skapa heta partitioner på infogningar effektivt frågorna klientprogrammet gör?  
* Betyder din förväntade mängden transaktioner att du förmodligen att nå skalbarhetsmål för en enskild partition och att begränsas av storage-tjänsten?  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Undvik prepend/lägga till ett mönstret när volymen av transaktioner är sannolikt att begränsar av storage-tjänsten när du använder en frekvent partition frekvensbegränsningen.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Mönster för log tail](#log-tail-pattern)  
* [Ändra entiteter](#modifying-entities)  

### <a name="log-data-anti-pattern"></a>Logga ett datamönster
Du bör normalt använda Blob-tjänsten i stället för Table service för att lagra loggdata.  

#### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt användningsfall för loggdata är att hämta en uppsättning loggposter för ett specifikt datum-/ tidsintervall: till exempel du vill hitta alla fel och kritiska meddelanden som ditt program loggas mellan 15:04 och 15:06 på ett visst datum. Du inte vill använda datum och tid loggmeddelandet för att fastställa den partition som du sparar log entiteter för att: som resulterar i en frekvent partition eftersom vid en given tidpunkt, så kommer alla log-entiteter dela samma **PartitionKey** värde (se den avsnittet [Prepend/lägga till ett mönster](#prepend-append-anti-pattern)). Följande entitetsschemat för ett loggmeddelande resulterar i en frekvent partition eftersom programmet skriver alla loggmeddelanden till partition för den aktuella datum och tid:  

![][28]

I det här exemplet på **RowKey** innehåller datum och tid för loggmeddelande så att loggmeddelanden lagras i datum/tid-ordning och innehåller ett meddelande-id om flera loggmeddelanden delar samma datum och tid.  

En annan metod är att använda en **PartitionKey** som säkerställer att programmet skriver meddelanden över flera olika partitioner. Om källan för loggmeddelandet är ett sätt att distribuera meddelanden över många partitioner, kan du till exempel använda följande entitetsschemat:  

![][29]

Problem med det här schemat är dock att om du vill hämta alla loggmeddelanden för en viss tidsrymd måste du söka varje partition i tabellen.

#### <a name="solution"></a>Lösning
Föregående avsnitt markerad problemet med försöker använda tabelltjänsten för att lagra loggposter och föreslagna två, otillfredsställande, design. En lösning som ledde till en frekvent partition med risk för prestandaproblem skriva loggmeddelanden; den andra lösningen resulterade i sämre prestanda på grund av kravet på att söka igenom alla partitioner i tabellen för att hämta loggmeddelanden för ett specifikt tidsintervall. BLOB-lagring erbjuder en bättre lösning för den här typen av scenario och det är hur Azure Storage Analytics butiker loggdata som samlas in.  

Det här avsnittet beskriver hur Storage Analytics lagrar loggdata i blob storage som en illustration av den här metoden för att lagra data som du vanligtvis fråga genom att intervallet.  

Lagringsanalys lagrar loggmeddelanden i en avgränsad format i flera blobar. Avgränsad format gör det enkelt för ett klientprogram att parsa data i loggmeddelandet.  

Storage Analytics använder en namngivningskonvention för blobbar som gör det möjligt för dig att hitta blob (eller blobbar) som innehåller loggmeddelanden som du söker. Till exempel innehåller en blob med namnet ”queue/2014/07/31/1800/000001.log” loggmeddelanden som relaterar till kötjänst för med början vid 18:00 31 juli 2014. ”000001” anger att detta är den första loggfilen för den här perioden. Lagringsanalys registrerar också tidsstämplarna för de första och sista loggmeddelanden som lagras i filen som en del av den blob-metadata. API för blob storage kan du hitta blobbar i en behållare baserat på ett namnprefix: Om du vill hitta alla blobbar som innehåller kön loggdata för med början vid 18:00, kan du använda prefixet ”kö/2014/07/31/1800”.  

Storage Analytics buffertar logga meddelanden internt och sedan regelbundet uppdatera lämpliga blob eller skapar en ny med den senaste batchen av loggposter. Detta minskar antalet skrivningar som måste utföras mot blob service.  

Om du implementerar en liknande lösning i ditt eget program, måste du tänka på hur du hanterar en kompromiss mellan tillförlitlighet (skriver varje loggpost till blob storage som händer) och kostnaden och skalbarhet (buffring uppdateringar i ditt program och skriva dem till blob storage i batchar).  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du lagrar loggdata:  

* Om du skapar en tabelldesign som undviker eventuella heta partitioner kan hända att du inte kommer åt loggdata effektivt.  
* Om du vill bearbeta loggdata, en klient behöver ofta läsa in många poster.  
* Även om loggdata är ofta strukturerade, kan blob-lagring vara en bättre lösning.  

### <a name="implementation-considerations"></a>Att tänka på vid implementering
Det här avsnittet beskrivs några saker att ha i åtanke när du implementerar de mönster som beskrivs i föregående avsnitt. De flesta av det här avsnittet använder exempel som skrivits i C# och som använder Storage-klientbibliotek (version 4.3.0 då skrivs).  

### <a name="retrieving-entities"></a>Hämtar entiteter
Enligt beskrivningen i avsnittet [Design för att fråga](#design-for-querying), mest effektivt fråga är en punkt-fråga. I vissa situationer kan du dock behöva hämta flera entiteter. Det här avsnittet beskrivs några vanliga metoder för att hämta entiteter med hjälp av Storage-klientbiblioteket.  

#### <a name="executing-a-point-query-using-the-storage-client-library"></a>Kör en punkt-fråga med hjälp av Storage-klientbibliotek
Det enklaste sättet att köra en punkt-fråga är att använda den **hämta** tabellen åtgärden som visas i C# kodfragment som hämtar en entitet med en **PartitionKey** värde ”försäljning” och en  **RowKey** värde ”212”:  

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

#### <a name="retrieving-multiple-entities-using-linq"></a>Hämtar flera entiteter med hjälp av LINQ
Du kan hämta flera entiteter med hjälp av LINQ med Storage-klientbiblioteket och ange en fråga med en **där** satsen. Om du vill undvika en tabellgenomsökning, bör du alltid skriva den **PartitionKey** värdet i where-satsen, och om möjligt den **RowKey** värde att undvika tabell och partition genomsökningar. Table service har stöd för en begränsad uppsättning jämförelseoperatorer (större än, större än eller lika med, mindre, mindre än eller lika med, lika med och inte lika med) att använda i where-satsen. I följande C#-kodavsnitt som söker efter alla anställda vars efternamn börjar med ”B” (förutsatt att den **RowKey** lagrar efternamn) på försäljningsavdelningen (förutsatt att den **PartitionKey** lagrar den avdelningsnamn):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Observera hur frågan anger både en **RowKey** och en **PartitionKey** säkerställa bättre prestanda.  

Följande kodexempel visar motsvarande funktioner med fluent-API (Mer information om fluent-API: er i allmänhet finns [bästa praxis för att utforma en Fluent-API](http://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

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
> Exemplet omsluter flera **CombineFilters** metoder för att inkludera tre filtervillkoren.  
> 
> 

#### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Hämtning av stora mängder entiteter från en fråga
En optimal frågan returnerar en enskild entitet baserat på en **PartitionKey** värde och ett **RowKey** värde. Du kan dock ha ett krav att returnera många entiteter från samma partition eller även många partitioner i vissa situationer.  

I sådana situationer bör du alltid helt testa programmets prestanda.  

En fråga till tabelltjänsten kan returnera högst 1 000 entiteter i taget och kan köras i högst fem sekunder. Om resultatet innehåller fler än 1 000 entiteter om frågan inte slutfördes inom fem sekunder, eller om frågan överskrider gränsen för partition, returnerar Table service ett fortsättningstoken för att aktivera klientprogram för att begära en uppsättning entiteter. Mer information om hur fortsättning tokens arbete finns i [tidsgräns för fråga och sidbrytning](http://msdn.microsoft.com/library/azure/dd135718.aspx).  

Om du använder Storage-klientbiblioteket, hanterar den automatiskt fortsättning token åt dig när den returnerar entiteter från tabelltjänsten. Följande C# kodexemplet använder Storage-klientbiblioteket automatiskt hanterar fortsättning token om table service returnerar dem i ett svar:  

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

Du kan styra när programmet hämtar data nästa segment med hjälp av fortsättning token uttryckligen. Till exempel om klientprogrammet gör att användarna kan bläddra igenom de entiteter som lagras i en tabell, en användare behöver inte igenom alla entiteter som hämtas av frågan, så att ditt program använder bara ett fortsättningstoken för att hämta nästa segmentera när användaren hade slutförts bläddring genom alla entiteter i det aktuella segmentet. Den här metoden har flera fördelar:  

* Du kan begränsa mängden data som ska hämtas från tabelltjänsten och att du flyttar över nätverket.  
* Det gör att du kan utföra asynkrona i/o i .NET.  
* Det gör att du kan serialisera fortsättningstoken till beständig lagring, så att du kan fortsätta i händelse av ett program systemkrasch.  

> [!NOTE]
> Ett fortsättningstoken returnerar vanligtvis ett segment som innehåller 1 000 entiteter, men det kan finnas färre. Detta gäller även om du begränsar antalet poster som en fråga som returnerar med hjälp av **ta** att returnera de första n entiteter som matchar sökvillkoren sökning: table service kan returnera ett segment som innehåller färre än n entiteter tillsammans med en fortsättningstoken så att du kan hämta de återstående entiteterna.  
> 
> 

Följande C#-kod visar hur du ändrar antalet enheter som returneras i ett segment:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projektion för serversidan
En enda entitet kan ha upp till 255 egenskaper och upp till 1 MB i storlek. När du frågar tabellen och hämta entiteter kan du kanske inte behöver alla egenskaper och kan undvika att överföra data onödan (om du vill minska svarstid och kostnader). Du kan använda serversidan projektion Överför bara de egenskaper som du behöver. I följande exempel hämtas bara de **e-post** egenskapen (tillsammans med **PartitionKey**, **RowKey**, **tidsstämpel**, och **ETag**) från de entiteter som valts av frågan.  

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

Observera hur **RowKey** värdet är tillgängliga även om den inte ingick i listan över egenskaper som hämtas.  

### <a name="modifying-entities"></a>Ändra entiteter
Storage-klientbiblioteket kan du ändra dina entiteter som lagras i tabelltjänsten genom att lägga till, ta bort och uppdatera entiteter. Du kan använda EGTs batch flera infogningar, uppdatera och ta borttagningsåtgärder tillsammans för att minska antalet sändningar som krävs och förbättra prestandan för din lösning.  

Undantag som utlöses när en EGT körs vanligtvis i Storage-klientbiblioteket omfattar index för den entitet som orsakade batch misslyckas. Detta är användbart när du felsöker kod som använder EGTs.  

Du bör också överväga hur din design påverkar hanteringen av samtidighet och uppdateringsåtgärderna i klientprogrammet.  

#### <a name="managing-concurrency"></a>Hantera samtidighet
Som standard tabelltjänsten implementerar Optimistisk samtidighet kontrollerar på nivån för enskilda entiteter för **infoga**, **sammanfoga**, och **ta bort** åtgärder, även om den är möjligt för en klient att tvinga tabelltjänsten att kringgå de här kontrollerna. Läs mer om hur tabelltjänsten hanterar samtidighet [hantera samtidighet i Microsoft Azure Storage](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Sammanfoga eller ersätta
Den **Ersätt** -metoden för den **TableOperation** klass ersätts alltid fullständig entitet i Table service. Om du inte använder en egenskap i begäran när egenskapen finns i entiteten lagrade begäran att tar bort egenskapen från den lagrade entiteten. Om du inte vill ta bort en egenskap uttryckligen från en lagrad entitet, måste du inkludera varje egenskap i begäran.  

Du kan använda den **sammanfoga** -metoden för den **TableOperation** klassen för att minska mängden data som du skickar till Tabelltjänsten igen när du vill uppdatera en entitet. Den **sammanfoga** metoden ersätter alla egenskaper i entiteten lagrade med egenskapsvärden från entiteten i begäran, men förblir intakta alla egenskaper i lagrade entiteten som inte ingår i begäran. Detta är användbart om du har stora entiteter och behöver bara uppdatera ett litet antal egenskaper i en begäran.  

> [!NOTE]
> Den **Ersätt** och **sammanfoga** metoder misslyckas om entiteten inte finns. Alternativt kan du använda den **InsertOrReplace** och **InsertOrMerge** metoder som skapar en ny entitet om det inte finns.  
> 
> 

### <a name="working-with-heterogeneous-entity-types"></a>Arbeta med heterogena entitetstyper
Table service är en *utan schema* tabellarkiv innebär det att en enda tabell kan lagra entiteter av flera typer som ger stor flexibilitet i din design. I följande exempel visas en tabell som lagrar både anställda och avdelning entiteter:  

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
<th>FirstName</th>
<th>LastName</th>
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
<th>FirstName</th>
<th>LastName</th>
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
<th>FirstName</th>
<th>LastName</th>
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

Varje entitet måste dock ha **PartitionKey**, **RowKey**, och **tidsstämpel** värden, men kan ha en uppsättning egenskaper. Det finns inget att ange vilken typ av en entitet om du inte väljer att lagra informationen någonstans. Det finns två alternativ för att identifiera entitetstypen:  

* Lägg till åtkomstgruppen entitetstypen till den **RowKey** (eller eventuellt den **PartitionKey**). Till exempel **EMPLOYEE_000123** eller **DEPARTMENT_SALES** som **RowKey** värden.  
* Använda en separat egenskap för att registrera enhetstypen som visas i tabellen nedan.  

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
<th>entityType</th>
<th>FirstName</th>
<th>LastName</th>
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
<th>entityType</th>
<th>FirstName</th>
<th>LastName</th>
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
<th>entityType</th>
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
<th>entityType</th>
<th>FirstName</th>
<th>LastName</th>
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

Det första alternativet, prepending entiteten skriver till den **RowKey**, är användbart om det finns en risk att två entiteter med olika typer kan ha samma nyckelvärde. Den också grupperar entiteter av samma typ tillsammans i partitionen.  

De metoder som beskrivs i det här avsnittet är särskilt relevanta till diskussionen [arvsrelationer](#inheritance-relationships) tidigare i den här guiden i avsnittet [modellering relationer](#modelling-relationships).  

> [!NOTE]
> Du bör inkludera ett versionsnummer i entiteten TYPVÄRDE och låt klientprogram att utvecklas POCO-objekt och arbeta med olika versioner.  
> 
> 

Resten av det här avsnittet beskrivs några av de funktioner i Storage-klientbiblioteket som underlättar arbetet med flera typer av enheter i samma tabell.  

#### <a name="retrieving-heterogeneous-entity-types"></a>Hämtning av heterogena entitetstyper
Om du använder Storage-klientbiblioteket, har du tre alternativ för att arbeta med flera typer av enheter.  

Om du vet vilken typ av enhet som lagras med ett visst **RowKey** och **PartitionKey** värden, kan du ange entitetstypen när du hämtar entitet som du ser i föregående två exempel som Hämta entiteter av typen **EmployeeEntity**: [kör en punkt-fråga med hjälp av Storage-klientbiblioteket](#executing-a-point-query-using-the-storage-client-library) och [hämtar flera entiteter med hjälp av LINQ](#retrieving-multiple-entities-using-linq).  

Det andra alternativet är att använda den **DynamicTableEntity** typ (en egenskapsuppsättning) i stället för en konkret POCO entitetstypen (det här alternativet kan också förbättra prestanda eftersom det finns inget behov att serialisera och deserialisera entiteten till .NET-typer). Följande C#-kod potentiellt hämtar flera entiteter av olika typer från tabellen, men returnerar alla entiteter som **DynamicTableEntity** instanser. Därefter använder den **EntityType** som bestämmer vilken typ av varje entitet:  

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

Om du vill hämta andra egenskaper, måste du använda den **TryGetValue** metoden på den **egenskaper** egenskapen för den **DynamicTableEntity** klass.  

Ett tredje alternativ är att kombinera med hjälp av den **DynamicTableEntity** typ och en **EntityResolver** instans. Detta gör det möjligt för dig att lösa flera POCO-typer i samma fråga. I det här exemplet på **EntityResolver** ombud använder den **EntityType** egenskapen att skilja mellan de två typerna av entiteten som returneras av frågan. Den **lösa** metoden används den **matchare** ombud för att lösa **DynamicTableEntity** -instanser till **TableEntity** instanser.  

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
Du behöver inte vet vilken typ av en entitet att ta bort den och du vet alltid vilken datatyp för en entitet när du infogar den. Du kan dock använda **DynamicTableEntity** Skriv för att uppdatera en entitet utan att känna till dess typ och utan att använda en POCO-entitetsklass. Följande kodexempel hämtas en enda entitet och kontrollerar de **EmployeeCount** egenskapen finns innan du uppdaterar den.  

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
Du kan använda token för signatur för delad åtkomst (SAS) för att aktivera klientprogram för att ändra (och fråga) tabellenheter direkt utan att behöva autentisera direkt med table service. Det finns vanligtvis tre huvudsakliga fördelar med SAS i ditt program:  

* Du behöver inte distribuera din lagringskontonyckel till ett osäkert plattform (till exempel en mobil enhet) för att tillåta att enheten för att komma åt och ändra entiteter i Table service.  
* Du omfördela några av det arbete som webb-och arbetsroller utföra i hanteringen av dina entiteter till klientenheter, till exempel slutanvändarnas datorer och mobila enheter.  
* Du kan tilldela en begränsad och tid begränsad uppsättning behörigheter till en klient (till exempel att tillåta skrivskyddad åtkomst till specifika resurser).  

Läs mer om hur du använder SAS-token med Table service, [med signaturer för delad åtkomst (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Du måste dock fortfarande generera SAS-token som ger ett klientprogram till entiteter i tabelltjänsten: göra detta i en miljö som har säker åtkomst till dina lagringskontonycklar. Normalt använder du en web- eller worker-roll för att generera SAS-token och skicka dem till klientprogram som behöver åtkomst till dina entiteter. Eftersom det finns fortfarande en belastning i genererar och leverera SAS-token till klienter, bör du hur du bäst för att minska den här kostnader, särskilt i scenarion med stora volymer.  

Det är möjligt att skapa en SAS-token som beviljar åtkomst till en delmängd entiteter i en tabell. Som standard kan du skapa en SAS-token för en hel tabell, men det är också möjligt att ange att SAS-token ger åtkomst till antingen en mängd **PartitionKey** värden, eller en mängd **PartitionKey** och **RowKey** värden. Du kan välja att generera SAS-token för enskilda användare i systemet så att varje användares SAS-token endast ger dem åtkomst till sina egna entiteter i table service.  

### <a name="asynchronous-and-parallel-operations"></a>Asynkron och parallella åtgärder
Förutsatt att dina förfrågningar sprids över flera partitioner, kan du förbättra svarstiden för dataflöde och klienten med hjälp av asynkrona eller parallella frågor.
Du kan till exempel ha två eller flera arbetsrollinstanser åtkomst till dina tabeller parallellt. Du kan ha enskilda worker-roller som är ansvarig för specifika uppsättningar med partitioner eller helt enkelt har flera arbetarrollinstanser, varje kan komma åt alla partitioner i en tabell.  

Du kan förbättra dataflödet genom att köra lagringsåtgärder asynkront i en client-instans. Storage-klientbiblioteket gör det enkelt att skriva asynkrona frågor och ändringar. Du kan till exempel börja med synkron metod som hämtar alla entiteter i en partition som visas i följande C#-kod:  

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

I den här asynkrona exempelvis ser du följande ändringar från den synkrona versionen:  

* Metodsignaturen innehåller nu den **async** modifieraren och returnerar en **uppgift** instans.  
* I stället för att anropa den **ExecuteSegmented** att hämta resultat, metoden nu anropar den **ExecuteSegmentedAsync** metoden och använder den **await** modifieraren till Hämta resultatet asynkront.  

Klientprogrammet kan anropa den här metoden flera gånger (med olika värden för den **avdelning** parametern), och varje fråga ska köras på en separat tråd.  

Det finns inga asynkron version av den **kör** -metod i den **TableQuery** klassen eftersom den **IEnumerable** gränssnittet stöder inte asynkrona uppräkning.  

Du kan också infoga, uppdatera och ta bort entiteter asynkront. I följande C#-exempel visas en enkel, synkron metod för att infoga eller ersätta en anställd entitet:  

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

I den här asynkrona exempelvis ser du följande ändringar från den synkrona versionen:  

* Metodsignaturen innehåller nu den **async** modifieraren och returnerar en **uppgift** instans.  
* I stället för att anropa den **kör** att uppdatera entiteten, metoden nu anropar den **ExecuteAsync** metoden och använder den **await** modifieraren att hämta resultat asynkront.  

Klientprogrammet kan anropa flera asynkrona metoder som det här och varje metodanropet ska köras på en separat tråd.  

### <a name="credits"></a>Eftertexter
Vi vill tacka följande medlemmarna i Azure-teamet för sina bidrag: Dominic Betts, Jason Hogg, Jean Ghanem, Jai Haridas, Jeff Irwin, Vamshidhar Kommineni, Vinay Shah Serdar Ozler samt och Tom Hollander från Microsoft DX. 

Vi vill även tacka följande Microsoft-MVPs för sina värdefull feedback under granskningscykler: Igor Papirov och Edward Bakker.

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


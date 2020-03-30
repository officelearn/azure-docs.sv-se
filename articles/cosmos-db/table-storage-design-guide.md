---
title: Designa Azure Cosmos DB-tabeller för skalning och prestanda
description: 'Designguide för Azure Table Storage: Skalbara och högpresterande tabeller i Azure Cosmos DB och Azure Table-lagring'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: sakash279
ms.author: akshanka
ms.custom: seodec18
ms.openlocfilehash: 166076d366cbbf7bef24648772beaba9b3a88253
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246479"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Designguide för Azure Table Storage Table: Skalbara och högpresterande tabeller

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

För att utforma skalbara och presterande tabeller måste du tänka på en rad olika faktorer, däribland kostnad. Om du tidigare har utformat scheman för relationsdatabaser är det viktigt att du är bekant med dessa överväganden. Det finns vissa likheter mellan Azure Table Storage och relationsdatabaser, men det finns också många viktiga skillnader. Dessa skillnader leder vanligtvis till olika design som kan se till att det är intuitivt eller fel i någon bekant relation mellan relationsdatabaser, men det är viktigt om du designar för ett NoSQL-nyckel/värde-lager, till exempel Table Storage.

Tabelllagring är utformad för att stödja molnskalningsprogram som kan innehålla miljarder entiteter ("rader" i relationsdatabasterminologi) av data eller för datauppsättningar som måste stödja stora transaktionsvolymer. Du måste därför tänka annorlunda på hur du lagrar dina data och förstå hur tabelllagring fungerar. Ett väl utformat NoSQL-datalager kan göra det möjligt för din lösning att skalas mycket längre (och till en lägre kostnad) än en lösning som använder en relationsdatabas. Den här guiden hjälper dig med dessa ämnen.  

## <a name="about-azure-table-storage"></a>Om Lagring av Azure-tabell
I det här avsnittet beskrivs några av de viktigaste funktionerna i tabelllagring som är särskilt relevanta för att utforma för prestanda och skalbarhet. Om du inte har använt Azure Storage and Table-lagring tidigare läser [du Introduktion till Microsoft Azure Storage](../storage/common/storage-introduction.md) och Kom igång med Azure Table Storage med hjälp av [.NET](table-storage-how-to-use-dotnet.md) innan du läser resten av den här artikeln. Även om fokus för den här guiden ligger på Tabelllagring, innehåller den en del diskussion om Azure Queue storage och Azure Blob storage och hur du kan använda dem tillsammans med Tabelllagring i en lösning.  

Tabelllagring använder ett tabellformat för att lagra data. I standardterminologin representerar varje rad i tabellen en entitet och kolumnerna lagrar de olika egenskaperna för den entiteten. Varje entitet har ett par nycklar för att unikt identifiera den och en tidsstämpelkolumn som tabelllagring använder för att spåra när entiteten senast uppdaterades. Tidsstämpelfältet läggs till automatiskt och du kan inte skriva över tidsstämpeln manuellt med ett godtyckligt värde. Tabelllagring använder den här senast ändrade tidsstämpeln (LMT) för att hantera optimistisk samtidighet.  

> [!NOTE]
> REST API-åtgärder för `ETag` tabelllagring returnerar också ett värde som det härleder från LMT. I det här dokumentet används termerna ETag och LMT omväxlande, eftersom de refererar till samma underliggande data.  
> 
> 

I följande exempel visas en enkel tabelldesign för att lagra medarbetare och avdelningsenheter. Många av de exempel som visas senare i den här guiden är baserade på denna enkla design.  

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
<td>Cao</td>
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
<th>DepartmentName</th>
<th>MedarbetareCount</th>
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


Hittills ser den här designen ut ungefär som en tabell i en relationsdatabas. De viktigaste skillnaderna är de obligatoriska kolumnerna och möjligheten att lagra flera entitetstyper i samma tabell. Dessutom har var och en av de användardefinierade egenskaperna, till exempel **Förnamn** eller **Ålder,** en datatyp, till exempel heltal eller sträng, precis som en kolumn i en relationsdatabas. Till skillnad från i en relationsdatabas innebär dock schemalös typ av tabelllagring att en egenskap inte behöver ha samma datatyp på varje entitet. Om du vill lagra komplexa datatyper i en enda egenskap måste du använda ett serialiserat format som JSON eller XML. Mer information finns i [Förstå datamodell för tabelllagring](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Ditt val `PartitionKey` `RowKey` av och är grundläggande för bra bordsdesign. Varje entitet som lagras i `PartitionKey` en `RowKey`tabell måste ha en unik kombination av och . Precis som med nycklar i en `PartitionKey` `RowKey` relationsdatabastabell indexeras värdena och för att skapa ett grupperat index som möjliggör snabba uppslag. Tabelllagring skapar dock inte några sekundära index, så det här är de enda två indexerade egenskaperna (vissa av de mönster som beskrivs senare visar hur du kan undvika den här uppenbara begränsningen).  

En tabell består av en eller flera partitioner, och många av de `PartitionKey` designbeslut du fattar kommer att vara runt att välja en lämplig och `RowKey` för att optimera din lösning. En lösning kan bestå av bara en enda tabell som innehåller alla dina entiteter ordnade i partitioner, men vanligtvis har en lösning flera tabeller. Tabeller hjälper dig att logiskt ordna dina entiteter och hjälper dig att hantera åtkomsten till data med hjälp av åtkomstkontrollistor. Du kan släppa en hel tabell med hjälp av en enda lagringsåtgärd.  

### <a name="table-partitions"></a>Tabellpartitioner
Kontonamnet, tabellnamnet och `PartitionKey` tillsammans identifiera partitionen i lagringstjänsten där Tabelllagring lagrar entiteten. Förutom att vara en del av adresssystemet för entiteter definierar partitioner ett scope för transaktioner (se avsnittet senare i den här artikeln, [Entitetsgruppstransaktioner)](#entity-group-transactions)och ligger till grund för hur Tabelllagring skalor. Mer information om tabellpartitioner finns i [Checklista för prestanda och skalbarhet för tabelllagring](../storage/tables/storage-performance-checklist.md).  

I Tabelllagring tjänsterar en enskild nod en eller flera fullständiga partitioner och tjänsten skalas genom dynamiskt belastningsutjämningspartitioner över noder. Om en nod är under belastning kan tabelllagring dela upp intervallet för partitioner som styrs av den noden på olika noder. När trafiken avtar kan tabelllagring slå samman partitionen från tysta noder tillbaka till en enda nod.  

Mer information om den interna informationen om tabelllagring, och i synnerhet hur den hanterar partitioner, finns i [Microsoft Azure Storage: En molnbaserad molnlagringstjänst med stark konsekvens](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Transaktioner i entitetsgrupp
I Tabelllagring är entitetsgruppstransaktioner (EGTs) den enda inbyggda mekanismen för att utföra atomuppdateringar över flera entiteter. Egts kallas också *batchtransaktioner*. EGTs kan bara fungera på entiteter som lagras i samma partition (dela samma partitionsnyckel i en viss tabell), så när du behöver atomisk transaktionsbeteende över flera entiteter, se till att dessa entiteter är i samma partition. Detta är ofta en anledning till att behålla flera entitetstyper i samma tabell (och partition) och inte använda flera tabeller för olika entitetstyper. En enda egt kan fungera på högst 100 enheter.  Om du skickar in flera samtidiga EGTs för bearbetning, är det viktigt att se till att dessa EGTs inte fungerar på enheter som är vanliga över EGTs. Annars riskerar du att fördröja bearbetningen.

EGTs introducerar också en potentiell kompromiss för dig att utvärdera i din design. Om du använder fler partitioner ökar skalbarheten för ditt program, eftersom Azure har fler möjligheter till belastningsutjämningsbegäranden över noder. Men detta kan begränsa möjligheten för ditt program att utföra atomära transaktioner och upprätthålla en stark konsekvens för dina data. Dessutom finns det specifika skalbarhetsmål på nivån för en partition som kan begränsa dataflödet för transaktioner som du kan förvänta dig för en enda nod.

Mer information om skalbarhetsmål för Azure-lagringskonton finns i [Skalbarhetsmål för standardlagringskonton](../storage/common/scalability-targets-standard-account.md). Mer information om skalbarhetsmål för Tabelllagring finns i [Skalbarhets- och prestandamål för tabelllagring](../storage/tables/scalability-targets.md). Senare avsnitt i den här guiden diskuterar olika designstrategier som hjälper dig att hantera kompromisser som den här, och diskutera hur du bäst väljer din partitionsnyckel baserat på de specifika kraven i klientprogrammet.  

### <a name="capacity-considerations"></a>Överväganden för kapaciteter
I följande tabell finns några av de nyckelvärden som du bör känna till när du utformar en tabelllagringslösning:  

| Total kapacitet för ett Azure-lagringskonto | 500 TB |
| --- | --- |
| Antal tabeller i ett Azure-lagringskonto |Begränsas endast av lagringskontots kapacitet. |
| Antal partitioner i en tabell |Begränsas endast av lagringskontots kapacitet. |
| Antal entiteter i en partition |Begränsas endast av lagringskontots kapacitet. |
| Storleken på en enskild enhet |Upp till 1 MB, med högst 255 `PartitionKey` `RowKey`egenskaper `Timestamp`(inklusive , och ). |
| Storleken på`PartitionKey` |En sträng upp till 1 KB i storlek. |
| Storleken på`RowKey` |En sträng upp till 1 KB i storlek. |
| Storleken på en entitetsgruppstransaktion |En transaktion kan innehålla högst 100 entiteter och nyttolasten måste vara mindre än 4 MB i storlek. En EGT kan bara uppdatera en entitet en gång. |

Mer information finns i [Förstå datamodellen för tabelltjänst](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Kostnadsöverväganden
Tabelllagring är relativt billigt, men du bör inkludera kostnadsuppskattningar för både kapacitetsanvändning och antalet transaktioner som en del av utvärderingen av en lösning som använder tabelllagring. I många fall är det dock en giltig metod att lagra denormaliserade eller duplicerade data för att förbättra lösningens prestanda eller skalbarhet. Mer information om priser finns i [Azure Storage-priser](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Riktlinjer för tabelldesign
I de här listorna sammanfattas några av de viktigaste riktlinjerna som du bör tänka på när du utformar tabellerna. Den här guiden behandlar dem alla mer i detalj senare. Dessa riktlinjer skiljer sig från de riktlinjer som du normalt följer för relationsdatabasdesign.  

Utforma ditt tabelllagring för att *kunna läsas* effektivt:

* **Design för frågor i lästunga program.** När du utformar tabellerna bör du tänka på de frågor (särskilt de latenskänsliga) som du ska köra innan du tänker på hur du ska uppdatera dina entiteter. Detta resulterar vanligtvis i en effektiv och högpresterande lösning.  
* **Ange `PartitionKey` både `RowKey` och i dina frågor.** *Punktfrågor* som dessa är de mest effektiva tabelllagringsfrågorna.  
* **Överväg att lagra dubbletter av entiteter.** Tabelllagring är billigt, så överväg att lagra samma entitet flera gånger (med olika nycklar), för att möjliggöra effektivare frågor.  
* **Överväg att avnormalisera dina data.** Tabell lagring är billigt, så överväg att avnormalisera dina data. Lagra sammanfattningsenheter så att frågor om aggregerade data bara behöver komma åt en enda entitet.  
* **Använd sammansatta nyckelvärden.** De enda nycklar `PartitionKey` du `RowKey`har är och . Använd till exempel sammansatta nyckelvärden för att aktivera alternativa nyckelsökvägar för åtkomst till entiteter.  
* **Använd frågeprojektion.** Du kan minska mängden data som du överför över nätverket genom att använda frågor som bara markerar de fält du behöver.  

Utforma ditt tabelllagring *write* för att vara skriveffektiv:  

* **Skapa inte heta partitioner.** Välj nycklar som gör att du kan sprida dina begäranden över flera partitioner när som helst.  
* **Undvik toppar i trafiken.** Fördela trafiken över en rimlig tidsperiod och undvik toppar i trafiken.
* **Skapa inte nödvändigtvis en separat tabell för varje typ av entitet.** När du behöver atomtransaktioner mellan entitetstyper kan du lagra dessa flera entitetstyper i samma partition i samma tabell.
* **Tänk på det maximala dataflödet du måste uppnå.** Du måste vara medveten om skalbarhetsmålen för tabelllagring och se till att din design inte gör att du överskrider dem.  

Senare i den här guiden ser du exempel som omsätter alla dessa principer i praktiken.  

## <a name="design-for-querying"></a>Utforma för frågor
Tabelllagring kan läsas intensivt, skriva intensivt eller en blandning av de två. I det här avsnittet beskrivs utformningen för att stödja läsåtgärder på ett effektivt sätt. Vanligtvis är en design som stöder läsåtgärder effektivt också effektiv för skrivåtgärder. Det finns dock ytterligare överväganden när du utformar för att stödja skrivåtgärder. Dessa beskrivs i nästa avsnitt, [Design för dataändring](#design-for-data-modification).

En bra utgångspunkt för att du ska kunna läsa data effektivt är att fråga "Vilka frågor kommer mitt program att behöva köra för att hämta de data som behövs?"  

> [!NOTE]
> Med Tabelllagring är det viktigt att få designen korrekt på framsidan, eftersom det är svårt och dyrt att ändra den senare. I en relationsdatabas är det till exempel ofta möjligt att lösa prestandaproblem genom att helt enkelt lägga till index i en befintlig databas. Det här är inte ett alternativ med tabelllagring.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>Hur ditt `PartitionKey` val `RowKey` av och påverkar frågeprestanda
Följande exempel förutsätter att tabelllagring lagrar medarbetarentiteter med följande `Timestamp` struktur (de flesta exempel utelämnar egenskapen för tydlighet):  

| Kolumnnamn | Datatyp |
| --- | --- |
| `PartitionKey`(Institutionens namn) |String |
| `RowKey`(ID för medarbetare) |String |
| `FirstName` |String |
| `LastName` |String |
| `Age` |Integer |
| `EmailAddress` |String |

Här är några allmänna riktlinjer för att utforma tabelllagringsfrågor. Filtersyntaxen som används i följande exempel kommer från REST API:et för tabelllagring. Mer information finns i [Frågeentiteter](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* En *punktfråga* är den mest effektiva sökningen att använda och rekommenderas för uppslag eller uppslag med stora volymer som kräver den lägsta svarstiden. En sådan fråga kan använda indexen för att hitta `PartitionKey` en `RowKey` enskild entitet effektivt genom att ange både värden och värden. Till exempel: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* Näst bäst är en *intervallfråga*. Den använder `PartitionKey`, och filtrerar `RowKey` på ett intervall av värden för att returnera mer än en entitet. `PartitionKey` Värdet identifierar en specifik partition `RowKey` och värdena identifierar en delmängd av entiteterna i den partitionen. Till exempel: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* Tredje bästa är en *partition scan*. Den använder `PartitionKey`, och filtrerar på en annan icke-nyckelgenskap och kan returnera mer än en entitet. Värdet `PartitionKey` identifierar en specifik partition och egenskapsvärdena väljer för en delmängd av entiteterna i partitionen. Till exempel: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* En *tabellsökning* innehåller inte `PartitionKey`, och är ineffektiv eftersom den söker igenom alla partitioner som utgör tabellen för alla matchande entiteter. Den utför en tabellsökning oavsett om filtret `RowKey`använder . Till exempel: `$filter=LastName eq 'Jones'`.  
* Azure Table storage frågor som returnerar `PartitionKey` flera `RowKey` entiteter sortera dem i och ordning. Om du vill undvika att tillgripa `RowKey` entiteterna i klienten väljer du en som definierar den vanligaste sorteringsordningen. Frågeresultat som returneras av Azure Table API i Azure Cosmos DB sorteras inte efter partitionsnyckel eller radnyckel. En detaljerad lista över funktionsskillnader finns [i skillnader mellan tabell-API i Azure Cosmos DB och Azure Table storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Om du**or**använder ett " eller `RowKey` " för att ange ett filter baserat på värden resulterar det i en partitionsgenomsökning och behandlas inte som en intervallfråga. Undvik därför frågor som använder filter `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`som: .  

Exempel på kod på klientsidan som använder storage-klientbiblioteket för att köra effektiva frågor finns i:  

* [Köra en punktfråga med hjälp av lagringsklientbiblioteket](#run-a-point-query-by-using-the-storage-client-library)
* [Hämta flera entiteter med hjälp av LINQ](#retrieve-multiple-entities-by-using-linq)
* [Projektion på serversidan](#server-side-projection)  

Exempel på kod på klientsidan som kan hantera flera entitetstyper som lagras i samma tabell finns i:  

* [Arbeta med heterogena entitetstyper](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Välj en lämplig`PartitionKey`
Ditt val `PartitionKey` av bör balansera behovet av att möjliggöra användning av EGTs (för att säkerställa konsekvens) mot kravet att distribuera dina entiteter över flera partitioner (för att säkerställa en skalbar lösning).  

På en extrem, du kan lagra alla dina entiteter i en enda partition. Men detta kan begränsa skalbarheten för din lösning och förhindra att tabelllagring kan belastningsutjämnas. På den andra extrema kan du lagra en entitet per partition. Detta är mycket skalbart och gör det möjligt för tabelllagring att belastningsbalansbegäranden, men hindrar dig från att använda entitetsgruppstransaktioner.  

En `PartitionKey` idealisk gör att du kan använda effektiva frågor och har tillräckligt med partitioner för att säkerställa att din lösning är skalbar. Vanligtvis upptäcker du att dina entiteter har en lämplig egenskap som distribuerar dina entiteter över tillräckliga partitioner.

> [!NOTE]
> I ett system som lagrar information om `UserID` användare eller `PartitionKey`anställda kan det till exempel vara bra. Du kan ha flera entiteter som använder en viss `UserID` partitionsnyckel. Varje entitet som lagrar data om en användare grupperas i en enda partition. Dessa enheter är tillgängliga via egts, samtidigt som de är mycket skalbara.
> 
> 

Det finns ytterligare överväganden i `PartitionKey` ditt val av som relaterar till hur du infogar, uppdaterar och tar bort entiteter. Mer information finns i [Design för dataändring](#design-for-data-modification) senare i den här artikeln.  

### <a name="optimize-queries-for-table-storage"></a>Optimera frågor för tabelllagring
Tabelllagring indexerar automatiskt dina entiteter med hjälp av `PartitionKey` värdena och `RowKey` i ett enda grupperat index. Detta är anledningen till att punktfrågor är de mest effektiva att använda. Det finns dock inga andra index än de på `PartitionKey` `RowKey`det klustrade indexet på och .

Många designer måste uppfylla kraven för att aktivera uppslag av entiteter baserat på flera kriterier. Till exempel att hitta medarbetarentiteter baserat på e-post, medarbetar-ID eller efternamn. Följande mönster i avsnittet [Tabelldesignmönster](#table-design-patterns) behandlar dessa typer av krav. Mönstren beskriver också olika sätt att arbeta runt det faktum att tabelllagring inte tillhandahåller sekundära index.  

* [Sekundärt indexmönster inom partition:](#intra-partition-secondary-index-pattern)Lagra flera kopior `RowKey` av varje entitet med hjälp av olika värden (i samma partition). Detta möjliggör snabba och effektiva uppslag och alternativa `RowKey` sorteringsorder med hjälp av olika värden.  
* [Sekundärt indexmönster mellan partitioner:](#inter-partition-secondary-index-pattern)Lagra flera `RowKey` kopior av varje entitet med hjälp av olika värden i separata partitioner eller i separata tabeller. Detta möjliggör snabba och effektiva uppslag och alternativa `RowKey` sorteringsorder med hjälp av olika värden.  
* [Indexentiteter mönstrar](#index-entities-pattern): Underhåll indexentiteter för att möjliggöra effektiva sökningar som returnerar listor över entiteter.  

### <a name="sort-data-in-table-storage"></a>Sortera data i tabelllagring

Tabelllagring returnerar frågeresultat sorterade i `PartitionKey` stigande ordning, baserat på och sedan efter `RowKey`.

> [!NOTE]
> Frågeresultat som returneras av Azure Table API i Azure Cosmos DB sorteras inte efter partitionsnyckel eller radnyckel. En detaljerad lista över funktionsskillnader finns [i skillnader mellan tabell-API i Azure Cosmos DB och Azure Table storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Tangenter i Tabelllagring är strängvärden. Om du vill vara säkra på att numeriska värden sorteras korrekt bör du konvertera dem till en fast längd och pada dem med nollor. Om till exempel det medarbetar-ID-värde som du använder som `RowKey` är ett heltalsvärde bör du konvertera medarbetar-ID **123** till **00000123**. 

Många program har krav på att använda data sorterade i olika order: till exempel sortera medarbetare efter namn eller genom att ansluta datum. Följande mönster i avsnittet [Tabelldesignmönster](#table-design-patterns) behandlar hur du växlar order för dina entiteter:  

* [Sekundärt indexmönster inom partition:](#intra-partition-secondary-index-pattern)Lagra flera kopior `RowKey` av varje entitet med hjälp av olika värden (i samma partition). Detta möjliggör snabba och effektiva uppslag och alternativa `RowKey` sorteringsorder med hjälp av olika värden.  
* [Sekundärt indexmönster mellan partitioner:](#inter-partition-secondary-index-pattern)Lagra flera `RowKey` kopior av varje entitet med hjälp av olika värden i separata partitioner i separata tabeller. Detta möjliggör snabba och effektiva uppslag och alternativa `RowKey` sorteringsorder med hjälp av olika värden.
* [Log tail pattern](#log-tail-pattern): Hämta n-entiteterna som `RowKey` senast lades till i en partition med hjälp av ett värde som sorterar i omvänd datum och tidsordning. *n*  

## <a name="design-for-data-modification"></a>Utforma för dataändring
Det här avsnittet fokuserar på designöverväganden för att optimera infogningar, uppdateringar och borttagningar. I vissa fall måste du utvärdera avvägningen mellan designer som optimerar för att fråga mot mönster som optimerar för dataändringar. Denna utvärdering liknar vad du gör i design för relationsdatabaser (även om teknikerna för att hantera design kompromisser är olika i en relationsdatabas). Avsnittet [Tabelldesignmönster](#table-design-patterns) beskriver några detaljerade designmönster för Tabelllagring och belyser några av dessa kompromisser. I praktiken kommer du att upptäcka att många designer som är optimerade för att fråga entiteter också fungerar bra för att ändra entiteter.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optimera prestanda för att infoga, uppdatera och ta bort åtgärder
Om du vill uppdatera eller ta bort en entitet måste du kunna identifiera den `PartitionKey` med hjälp av värdena och. `RowKey` I detta avseende bör `PartitionKey` `RowKey` ditt val av och för att ändra entiteter följa liknande kriterier som ditt val för att stödja punktfrågor. Du vill identifiera entiteter så effektivt som möjligt. Du vill inte använda en ineffektiv partition eller tabellgenomsökning för att `PartitionKey` hitta `RowKey` en entitet för att upptäcka de värden som du behöver för att uppdatera eller ta bort den.  

Följande mönster i avsnittet [Tabelldesignmönster](#table-design-patterns) behandlar optimering av prestanda för dina infognings-, uppdaterings- och borttagningsåtgärder:  

* [Borttagningsmönster](#high-volume-delete-pattern)med hög volym : Aktivera borttagning av en hög mängd entiteter genom att lagra alla entiteter för samtidig borttagning i sin egen separata tabell. Du tar bort entiteterna genom att ta bort tabellen.  
* [Dataseriemönster](#data-series-pattern): Lagra fullständiga dataserier i en enda entitet för att minimera antalet begäranden du gör.  
* [Brett entitetsmönster](#wide-entities-pattern): Använd flera fysiska entiteter för att lagra logiska entiteter med mer än 252 egenskaper.  
* [Stort entitetsmönster](#large-entities-pattern): Använd blob-lagring för att lagra stora egenskapsvärden.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Se till att dina lagrade enheter är konsekventa
Den andra nyckelfaktorn som påverkar ditt val av nycklar för att optimera dataändringar är hur du säkerställer konsekvens med hjälp av atomtransaktioner. Du kan bara använda en EGT för att fungera på entiteter som lagras i samma partition.  

Följande mönster i avsnittet [Tabelldesignmönster](#table-design-patterns) behandlar konsekvens:  

* [Sekundärt indexmönster inom partition:](#intra-partition-secondary-index-pattern)Lagra flera kopior `RowKey` av varje entitet med hjälp av olika värden (i samma partition). Detta möjliggör snabba och effektiva uppslag och alternativa `RowKey` sorteringsorder med hjälp av olika värden.  
* [Sekundärt indexmönster mellan partitioner:](#inter-partition-secondary-index-pattern)Lagra flera `RowKey` kopior av varje entitet med hjälp av olika värden i separata partitioner eller i separata tabeller. Detta möjliggör snabba och effektiva uppslag och alternativa `RowKey` sorteringsorder med hjälp av olika värden.  
* [Så småningom konsekventa transaktionsmönster:](#eventually-consistent-transactions-pattern)Aktivera så småningom konsekvent beteende över partitionsgränser eller lagringssystemgränser med hjälp av Azure-köer.
* [Indexentiteter mönstrar](#index-entities-pattern): Underhåll indexentiteter för att möjliggöra effektiva sökningar som returnerar listor över entiteter.  
* [Denormaliseringsmönster](#denormalization-pattern): Kombinera relaterade data tillsammans i en enda entitet, så att du kan hämta alla data du behöver med en enda punktfråga.  
* [Dataseriemönster](#data-series-pattern): Lagra fullständiga dataserier i en enda entitet för att minimera antalet begäranden du gör.  

Mer information finns i [Transaktioner för entitetsgrupper](#entity-group-transactions) senare i den här artikeln.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Se till att din design för effektiva ändringar underlättar effektiva frågor
I många fall resulterar en design för effektiv fråga i effektiva ändringar, men du bör alltid utvärdera om detta är fallet för ditt specifika scenario. Vissa av mönstren i avsnittet [Tabelldesignmönster utvärderar](#table-design-patterns) uttryckligen kompromisser mellan att fråga och ändra entiteter, och du bör alltid ta hänsyn till antalet för varje typ av åtgärd.  

Följande mönster i avsnittet [Tabelldesignmönster](#table-design-patterns) tar upp kompromisser mellan att utforma för effektiva frågor och att utforma för effektiv dataändring:  

* [Sammansattnyckelmönster](#compound-key-pattern): `RowKey` Använd sammansatta värden för att en klient ska kunna slå upp relaterade data med en enda punktfråga.  
* [Log tail pattern](#log-tail-pattern): Hämta n-entiteterna som `RowKey` senast lades till i en partition med hjälp av ett värde som sorterar i omvänd datum och tidsordning. *n*  

## <a name="encrypt-table-data"></a>Kryptera tabelldata
.NET Azure Storage-klientbiblioteket stöder kryptering av strängentitetsegenskaper för att infoga och ersätta åtgärder. De krypterade strängarna lagras på tjänsten som binära egenskaper och de konverteras tillbaka till strängar efter dekryptering.    

För tabeller måste användarna, utöver krypteringsprincipen, ange vilka egenskaper som ska krypteras. Ange antingen `EncryptProperty` ett attribut (för POCO-entiteter som härleds från `TableEntity`) eller ange en krypteringsmatchningsmatchning i begäransalternativ. En krypteringsmatchningsordnare är ett ombud som tar en partitionsnyckel, radnyckel och egenskapsnamn och returnerar en boolesk som anger om egenskapen ska krypteras. Under krypteringen använder klientbiblioteket den här informationen för att avgöra om en egenskap ska krypteras när du skriver till kabeln. Ombudet ger också möjlighet till logik kring hur egenskaper krypteras. (Om du till exempel X krypterar du egenskap A; krypterar annars egenskaperna A och B.) Det är inte nödvändigt att ange den här informationen när du läser eller frågar entiteter.

Sammanfogning stöds för närvarande inte. Eftersom en delmängd av egenskaper kan ha krypterats tidigare med hjälp av en annan nyckel, kommer det att leda till dataförlust genom att bara slå samman de nya egenskaperna och uppdatera metadata. Sammanslagningen måste antingen göra extra serviceanrop för att läsa den befintliga entiteten från tjänsten eller använda en ny nyckel per egenskap. Ingen av dessa är lämpliga av prestandaskäl.     

Information om kryptering av tabelldata finns i [Kryptering på klientsidan och Azure Key Vault för Microsoft Azure Storage](../storage/common/storage-client-side-encryption.md).  

## <a name="model-relationships"></a>Modellrelationer
Att bygga domänmodeller är ett viktigt steg i utformningen av komplexa system. Vanligtvis använder du modelleringsprocessen för att identifiera entiteter och relationerna mellan dem, som ett sätt att förstå affärsdomänen och informera systemets design. Det här avsnittet fokuserar på hur du kan översätta några av de vanliga relationstyperna som finns i domänmodeller till design för tabelllagring. Processen att mappa från en logisk datamodell till en fysisk NoSQL-baserad datamodell skiljer sig från den som används när en relationsdatabas utformas. Relationsdatabaser design förutsätter vanligtvis en datanormaliseringsprocess optimerad för att minimera redundans. Sådan design förutsätter också en deklarativ frågefunktion som abstraherar implementeringen av hur databasen fungerar.  

### <a name="one-to-many-relationships"></a>En-till-många-relationer
1:N-relationer mellan affärsdomänobjekt förekommer ofta: till exempel har en avdelning många anställda. Det finns flera sätt att implementera 1:1:a-relationer i tabelllagring, var och en med för- och nackdelar som kan vara relevanta för det specifika scenariot.  

Tänk på exemplet med ett stort multinationellt företag med tiotusentals avdelningar och anställdaheter. Varje avdelning har många anställda och varje medarbetare är associerad med en specifik avdelning. En metod är att lagra separata avdelnings- och medarbetarenheter, till exempel följande:  

![Bild som visar en avdelningsenhet och en medarbetarentitet][1]

I det här exemplet visas en implicit 1:1:00-relation mellan typerna, baserat på `PartitionKey` värdet. Varje avdelning kan ha många anställda.  

I det här exemplet visas också en avdelningsentitet och dess relaterade medarbetarentiteter i samma partition. Du kan välja att använda olika partitioner, tabeller eller till och med lagringskonton för de olika entitetstyperna.  

En alternativ metod är att denormalisera dina data och lagra endast medarbetare med denormaliserade avdelningsdata, som visas i följande exempel. I det här scenariot kanske den här denormaliserade metoden inte är den bästa om du har ett krav på att kunna ändra information om en avdelningschef. För att göra detta måste du uppdatera alla medarbetare på avdelningen.  

![Grafik över medarbetarens entitet][2]

Mer information finns i [denormaliseringsmönstret](#denormalization-pattern) senare i den här guiden.  

I följande tabell sammanfattas för- och nackdelarna för var och en av metoderna för att lagra medarbetare och avdelningsenheter som har en 1:1:00-relation. Du bör också överväga hur ofta du förväntar dig att utföra olika operationer. Det kan vara acceptabelt att ha en design som innehåller en dyr åtgärd om den åtgärden bara sker sällan.  

<table>
<tr>
<th>Metod</th>
<th>Fördelar</th>
<th>Nackdelar</th>
</tr>
<tr>
<td>Separata entitetstyper, samma partition, samma tabell</td>
<td>
<ul>
<li>Du kan uppdatera en avdelningsentitet med en enda åtgärd.</li>
<li>Du kan använda en EGT för att upprätthålla konsekvens om du har ett krav på att ändra en avdelningsentitet när du uppdaterar/infogar/tar bort en medarbetarentitet. Om du till exempel har ett antal medarbetare på avdelningen för varje avdelning.</li>
</ul>
</td>
<td>
<ul>
<li>Du kan behöva hämta både en medarbetare och en avdelningsentitet för vissa klientaktiviteter.</li>
<li>Lagringsåtgärder sker i samma partition. Vid höga transaktionsvolymer kan detta resultera i en hotspot.</li>
<li>Du kan inte flytta en medarbetare till en ny avdelning med hjälp av en EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Separata entitetstyper, olika partitioner eller tabeller eller lagringskonton</td>
<td>
<ul>
<li>Du kan uppdatera en avdelningsentitet eller entitet med en enda operation.</li>
<li>Vid höga transaktionsvolymer kan detta hjälpa till att sprida belastningen över fler partitioner.</li>
</ul>
</td>
<td>
<ul>
<li>Du kan behöva hämta både en medarbetare och en avdelningsentitet för vissa klientaktiviteter.</li>
<li>Du kan inte använda EGTs för att upprätthålla konsekvens när du uppdaterar/infogar/tar bort en medarbetare och uppdaterar en avdelning. Till exempel uppdatera ett antal medarbetare i en avdelningsentitet.</li>
<li>Du kan inte flytta en medarbetare till en ny avdelning med hjälp av en EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalisera till enentitetstyp</td>
<td>
<ul>
<li>Du kan hämta all information du behöver med en enda begäran.</li>
</ul>
</td>
<td>
<ul>
<li>Det kan vara dyrt att upprätthålla konsekvens om du behöver uppdatera avdelningsinformation (detta skulle kräva att du uppdaterar alla anställda på en avdelning).</li>
</ul>
</td>
</tr>
</table>

Hur du väljer bland dessa alternativ, och vilka av för-och nackdelar är mest betydande, beror på dina specifika programscenarier. Hur ofta ändrar du till exempel avdelningsentiteter? Behöver alla dina medarbetarfrågor ytterligare avdelningsinformation? Hur nära är du skalbarhetsgränserna för dina partitioner eller ditt lagringskonto?  

### <a name="one-to-one-relationships"></a>En-till-en-relationer
Domänmodeller kan innehålla en-till-en-relationer mellan entiteter. Om du behöver implementera en 1:1-relation i tabelllagring måste du också välja hur du vill länka de två relaterade entiteterna när du behöver hämta dem båda. Den här länken kan antingen vara implicit, baserat på en konvention i nyckelvärdena, eller explicit, genom att lagra en länk i form av `PartitionKey` och `RowKey` värden i varje entitet till dess relaterade entitet. En diskussion om du ska lagra relaterade entiteter i samma partition finns i avsnittet [1:1:1-relationer](#one-to-many-relationships).  

Det finns också implementeringsöverväganden som kan leda till att du implementerar en-till-en-relationer i Tabelllagring:  

* Hantering av stora entiteter (mer information finns i [Mönster för stora entiteter](#large-entities-pattern)).  
* Implementera åtkomstkontroller (mer information finns i [Kontrollera åtkomst med signaturer](#control-access-with-shared-access-signatures)för delad åtkomst ).  

### <a name="join-in-the-client"></a>Gå med i klienten
Även om det finns sätt att modellera relationer i Tabelllagring, glöm inte att de två främsta orsakerna till att använda Tabelllagring är skalbarhet och prestanda. Om du upptäcker att du modellerar många relationer som äventyrar lösningens prestanda och skalbarhet bör du fråga dig själv om det är nödvändigt att skapa alla datarelationer i tabelldesignen. Du kanske kan förenkla designen och förbättra lösningens skalbarhet och prestanda om du låter klientprogrammet utföra nödvändiga kopplingar.  

Om du till exempel har små tabeller som innehåller data som inte ändras ofta kan du hämta dessa data en gång och cachelagra dem på klienten. På så sätt kan du undvika upprepade tur- och returturer för att hämta samma data. I de exempel som vi har tittat på i den här guiden är det troligt att antalet avdelningar i en liten organisation är små och ändras sällan. Detta gör det till en bra kandidat för data som ett klientprogram kan hämta en gång och cachelagra som uppslagsdata.  

### <a name="inheritance-relationships"></a>Arvsrelationer
Om klientprogrammet använder en uppsättning klasser som ingår i en arvsrelation för att representera affärsentiteter kan du enkelt spara dessa entiteter i Tabelllagring. Du kan till exempel ha följande uppsättning klasser definierade `Person` i klientprogrammet, där är en abstrakt klass.

![Diagram över arvsrelationer][3]

Du kan behålla förekomster av de två betongklasserna i Tabelllagring med hjälp av en enda `Person` tabell. Använd entiteter som ser ut så här:  

![Bild som visar kundentiteten och medarbetarens entitet][4]

Mer information om hur du arbetar med flera entitetstyper i samma tabell i klientkoden finns i [Arbeta med heterogena entitetstyper](#work-with-heterogeneous-entity-types) senare i den här guiden. Detta ger exempel på hur du känner igen entitetstypen i klientkoden.  

## <a name="table-design-patterns"></a>Mönster för tabelldesign
I föregående avsnitt har du lärt dig hur du optimerar tabelldesignen för både hämtning av entitetsdata med hjälp av frågor och för att infoga, uppdatera och ta bort entitetsdata. I det här avsnittet beskrivs några mönster som är lämpliga att använda med tabelllagring. Dessutom ser du hur du praktiskt taget kan ta itu med några av de frågor och kompromisser som tagits upp tidigare i den här guiden. I följande diagram sammanfattas relationerna mellan de olika mönstren:  

![Diagram över tabelldesignmönster][5]

Mönsterkartan belyser vissa relationer mellan mönster (blå) och anti-mönster (orange) som dokumenteras i den här guiden. Det finns naturligtvis många andra mönster som är värda att överväga. Ett av de viktigaste scenarierna för tabelllagring är till exempel att använda det [materialiserade vymönstret](https://msdn.microsoft.com/library/azure/dn589782.aspx) från [kommandofrågans ansvarssegregeringsmönster.](https://msdn.microsoft.com/library/azure/jj554200.aspx)  

### <a name="intra-partition-secondary-index-pattern"></a>Sekundärt indexmönster inom partitionen
Lagra flera kopior av varje `RowKey` entitet med hjälp av olika värden (i samma partition). Detta möjliggör snabba och effektiva uppslag och alternativa `RowKey` sorteringsorder med hjälp av olika värden. Uppdateringar mellan kopior kan hållas konsekventa med hjälp av egts.  

#### <a name="context-and-problem"></a>Kontext och problem
Tabelllagring indexerar automatiskt entiteter med hjälp av `PartitionKey` värdena och. `RowKey` Detta gör det möjligt för ett klientprogram att hämta en entitet effektivt med hjälp av dessa värden. Med hjälp av följande tabellstruktur kan ett klientprogram till exempel använda en punktfråga för att hämta `PartitionKey` en `RowKey` enskild medarbetarentitet med hjälp av avdelningsnamnet och medarbetar-ID :et (och värden). En klient kan också hämta entiteter sorterade efter medarbetar-ID inom varje avdelning.

![Grafik över medarbetarens entitet][6]

Om du också vill hitta en medarbetarentitet baserat på värdet av en annan egenskap, till exempel e-postadress, måste du använda en mindre effektiv partitionsgenomsökning för att hitta en matchning. Detta beror på att tabelllagring inte ger sekundära index. Dessutom finns det inget alternativ för att begära en lista över `RowKey` medarbetare sorterade i en annan ordning än i ordning.  

#### <a name="solution"></a>Lösning
Om du vill gå runt på bristen på sekundära index kan du lagra `RowKey` flera kopior av varje entitet, där varje kopia använder ett annat värde. Om du lagrar en entitet med följande strukturer kan du effektivt hämta medarbetarentiteter baserat på e-postadress eller medarbetar-ID. Prefixvärdena `RowKey`för `empid_`, `email_` och gör att du kan fråga efter en enskild medarbetare, eller ett intervall av medarbetare, med hjälp av ett intervall med e-postadresser eller medarbetar-ID.  

![Bild som visar medarbetarens entitet med varierande RowKey-värden][7]

Följande två filtervillkor (ett som söker upp med medarbetar-ID och ett som söker upp via e-postadress) anger båda punktfrågor:  

* $filter=(PartitionKey eq 'Sales') och (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Sales') och (RowKey eq 'email_jonesj@contoso.com')  

Om du frågar efter ett intervall med medarbetarentiteter kan du ange ett intervall sorterat i medarbetar-ID-order eller ett intervall sorterat i e-postadressordning. Fråga efter entiteter med `RowKey`lämpligt prefix i .  

* Om du vill hitta alla anställda på försäljningsavdelningen med ett medarbetar-ID i intervallet 000100 till 000199 använder du: $filter=(PartitionKey eq 'Sales') och (RowKey ge 'empid_000100') och (RowKey le 'empid_000199')  
* Om du vill hitta alla anställda på försäljningsavdelningen med en e-postadress som börjar med bokstaven "a" använder du: $filter=(PartitionKey eq 'Sales') och (RowKey ge 'email_a') och (RowKey lt 'email_b')  
  
Filtersyntaxen som används i föregående exempel kommer från REST API:et för tabelllagring. Mer information finns i [Frågeentiteter](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Tabelllagring är relativt billigt att använda, så kostnaden omkostnader för att lagra dubbla data bör inte vara ett stort problem. Du bör dock alltid utvärdera kostnaden för din design baserat på dina förväntade lagringskrav och bara lägga till dubblettentiteter för att stödja de frågor som klientprogrammet kommer att köra.  
* Eftersom de sekundära indexentiteterna lagras i samma partition som de ursprungliga entiteterna, se till att du inte överskrider skalbarhetsmålen för en enskild partition.  
* Du kan hålla dina dubbla entiteter konsekventa med varandra genom att använda EGTs för att uppdatera de två kopiorna av entiteten atomärt. Detta innebär att du bör lagra alla kopior av en entitet i samma partition. Mer information finns i [Använda entitetsgruppstransaktioner](#entity-group-transactions).  
* Värdet som används `RowKey` för måste vara unikt för varje entitet. Överväg att använda sammansatta nyckelvärden.  
* Utfyllnad numeriska `RowKey` värden i (till exempel medarbetar-ID 000223) möjliggör korrekt sortering och filtrering baserat på övre och nedre gränser.  
* Du behöver inte nödvändigtvis duplicera alla egenskaper för din entitet. Om till exempel de frågor som slår upp entiteterna `RowKey` med hjälp av e-postadressen i aldrig behöver medarbetarens ålder, kan dessa entiteter ha följande struktur:

  ![Grafik över medarbetarens entitet][8]

* Vanligtvis är det bättre att lagra dubblettdata och se till att du kan hämta alla data du behöver med en enda fråga än att använda en fråga för att hitta en entitet och en annan för att slå upp nödvändiga data.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret i sådana här scenarier:

- Klientprogrammet måste hämta entiteter med hjälp av en mängd olika nycklar.
- Klienten måste hämta entiteter i olika sorteringsorder.
- Du kan identifiera varje entitet med hjälp av en mängd unika värden.

Se dock till att du inte överskrider gränserna för partitionskalbarhet när du utför `RowKey` entitetssökningar med hjälp av de olika värdena.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Sekundärt indexmönster mellan partitioner](#inter-partition-secondary-index-pattern)
* [Sammansattnyckelmönster](#compound-key-pattern)
* [Transaktioner i entitetsgrupp](#entity-group-transactions)
* [Arbeta med heterogena entitetstyper](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Sekundärt indexmönster mellan partitioner
Lagra flera kopior av varje `RowKey` entitet med hjälp av olika värden i separata partitioner eller i separata tabeller. Detta möjliggör snabba och effektiva uppslag och alternativa `RowKey` sorteringsorder med hjälp av olika värden.  

#### <a name="context-and-problem"></a>Kontext och problem
Tabelllagring indexerar automatiskt entiteter med hjälp av `PartitionKey` värdena och. `RowKey` Detta gör det möjligt för ett klientprogram att hämta en entitet effektivt med hjälp av dessa värden. Med hjälp av följande tabellstruktur kan ett klientprogram till exempel använda en punktfråga för att hämta `PartitionKey` en `RowKey` enskild medarbetarentitet med hjälp av avdelningsnamnet och medarbetar-ID :et (och värden). En klient kan också hämta entiteter sorterade efter medarbetar-ID inom varje avdelning.  

![Grafik över medarbetarens entitet][9]

Om du också vill kunna hitta en medarbetarentitet baserat på värdet av en annan egenskap, till exempel e-postadress, måste du använda en mindre effektiv partitionsgenomsökning för att hitta en matchning. Detta beror på att tabelllagring inte ger sekundära index. Dessutom finns det inget alternativ för att begära en lista över `RowKey` medarbetare sorterade i en annan ordning än i ordning.  

Du förväntar dig en hög transaktionsvolym mot dessa entiteter och vill minimera risken för att tabelllagringshastigheten begränsar klienten.  

#### <a name="solution"></a>Lösning
Om du vill gå runt på bristen på sekundära index kan du `PartitionKey` lagra `RowKey` flera kopior av varje entitet, där varje kopia används med olika värden och värden. Om du lagrar en entitet med följande strukturer kan du effektivt hämta medarbetarentiteter baserat på e-postadress eller medarbetar-ID. Prefixvärdena `PartitionKey`för `empid_`, `email_` och gör att du kan identifiera vilket index du vill använda för en fråga.  

![Bild som visar medarbetarens entitet med primärt index och entiteten medarbetare med sekundärt index][10]

Följande två filtervillkor (ett som söker upp med medarbetar-ID och ett som söker upp via e-postadress) anger båda punktfrågor:  

* $filter=(PartitionKey eq 'empid_Sales') och (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') och (RowKey eqjonesj@contoso.com' ')  

Om du frågar efter ett intervall med medarbetarentiteter kan du ange ett intervall sorterat i medarbetar-ID-order eller ett intervall sorterat i e-postadressordning. Fråga efter entiteter med `RowKey`lämpligt prefix i .  

* Om du vill hitta alla anställda på försäljningsavdelningen med ett medarbetar-ID i intervallet **000100** till **000199**, sorterade i medarbetar-ID-order, använder du: $filter=(PartitionKey eq 'empid_Sales') och (RowKey ge '000100' och (RowKey le '000199')  
* Om du vill hitta alla anställda på försäljningsavdelningen med en e-postadress som börjar med "a", sorterad i e-postadressordning, använder du: $filter=(PartitionKey eq 'email_Sales') och (RowKey ge 'a') och (RowKey lt 'b')  

Observera att filtersyntaxen som används i föregående exempel kommer från REST API:et för tabelllagring. Mer information finns i [Frågeentiteter](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du kan hålla dina dubbla entiteter så småningom konsekventa med varandra genom att använda [mönstret Så småningom konsekventa transaktioner](#eventually-consistent-transactions-pattern) för att underhålla de primära och sekundära indexentiteterna.  
* Tabelllagring är relativt billigt att använda, så kostnaden för att lagra dubbla data bör inte vara ett stort problem. Utvärdera dock alltid kostnaden för din design baserat på dina förväntade lagringskrav och lägg bara till dubblettentiteter som stöder de frågor som klientprogrammet kommer att köra.  
* Värdet som används `RowKey` för måste vara unikt för varje entitet. Överväg att använda sammansatta nyckelvärden.  
* Utfyllnad numeriska `RowKey` värden i (till exempel medarbetar-ID 000223) möjliggör korrekt sortering och filtrering baserat på övre och nedre gränser.  
* Du behöver inte nödvändigtvis duplicera alla egenskaper för din entitet. Om till exempel de frågor som slår upp entiteterna `RowKey` med hjälp av e-postadressen i aldrig behöver medarbetarens ålder, kan dessa entiteter ha följande struktur:
  
  ![Bild som visar medarbetarens entitet med sekundärt index][11]
* Vanligtvis är det bättre att lagra dubblettdata och se till att du kan hämta alla data du behöver med en enda fråga än att använda en fråga för att hitta en entitet med hjälp av det sekundära indexet och en annan för att slå upp nödvändiga data i det primära indexet.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret i sådana här scenarier:

- Klientprogrammet måste hämta entiteter med hjälp av en mängd olika nycklar.
- Klienten måste hämta entiteter i olika sorteringsorder.
- Du kan identifiera varje entitet med hjälp av en mängd unika värden.

Använd det här mönstret när du vill undvika att överskrida gränserna för `RowKey` partitionskalbarhet när du utför entitetssökningar med hjälp av de olika värdena.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Så småningom konsekvent transaktionsmönster](#eventually-consistent-transactions-pattern)  
* [Sekundärt indexmönster inom partitionen](#intra-partition-secondary-index-pattern)  
* [Sammansattnyckelmönster](#compound-key-pattern)  
* [Transaktioner i entitetsgrupp](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Så småningom konsekvent transaktionsmönster
Aktivera så småningom konsekvent beteende över partitionsgränser eller lagringssystemgränser med hjälp av Azure-köer.  

#### <a name="context-and-problem"></a>Kontext och problem
EGTs aktivera atomära transaktioner över flera entiteter som delar samma partitionsnyckel. Av prestanda- och skalbarhetsskäl kan du välja att lagra entiteter som har konsekvenskrav i separata partitioner eller i ett separat lagringssystem. I ett sådant scenario kan du inte använda EGTs för att upprätthålla konsekvens. Du kan till exempel ha ett krav på att upprätthålla eventuell överensstämmelse mellan:  

* Entiteter som lagras i två olika partitioner i samma tabell, i olika tabeller eller i olika lagringskonton.  
* En entitet som lagras i Tabelllagring och en blob som lagras i Blob-lagring.  
* En entitet som lagras i Tabelllagring och en fil i ett filsystem.  
* En entitet som lagras i Tabelllagring, men som indexeras med hjälp av Azure Cognitive Search.  

#### <a name="solution"></a>Lösning
Genom att använda Azure-köer kan du implementera en lösning som ger slutlig konsekvens mellan två eller flera partitioner eller lagringssystem.

För att illustrera den här metoden antar du att du har ett krav för att kunna arkivera tidigare medarbetare. Tidigare anställdaheter efterfrågas sällan och bör uteslutas från aktiviteter som hanterar aktuella medarbetare. Om du vill implementera det här kravet lagrar du aktiva medarbetare i tabellen **Aktuell och** tidigare medarbetare i arkivtabellen. **Archive** Arkivering av en medarbetare kräver att du tar bort entiteten från tabellen **Aktuell** och lägger till entiteten i **arkivtabellen.**

Men du kan inte använda en EGT för att utföra dessa två operationer. För att undvika risken för att ett fel gör att en entitet visas i båda eller båda tabellerna måste arkivåtgärden så småningom vara konsekvent. I följande sekvensdiagram beskrivs stegen i den här åtgärden.  

![Lösningsdiagram för eventuell konsekvens][12]

En klient initierar arkivåtgärden genom att placera ett meddelande i en Azure-kö (i det här exemplet för att arkivera medarbetare #456). En arbetarroll avbländerar kön efter nya meddelanden. när den hittar en, läser det meddelandet och lämnar en dold kopia i kön. Arbetarrollen hämtar sedan en kopia av entiteten från tabellen **Aktuell,** infogar en kopia i **arkivtabellen** och tar sedan bort originalet från tabellen **Aktuell.** Slutligen, om det inte fanns några fel från föregående steg, tar arbetarrollen bort det dolda meddelandet från kön.  

I det här exemplet infogar steg 4 **Archive** i diagrammet medarbetaren i arkivtabellen. Den kan lägga till medarbetaren i en blob i Blob-lagring eller en fil i ett filsystem.  

#### <a name="recover-from-failures"></a>Återställa från fel
Det är viktigt att åtgärderna i steg 4-5 i diagrammet är *idempotenta* om arbetarrollen behöver starta om arkivåtgärden. Om du använder Tabelllagring bör du för steg 4 använda en "infoga eller ersätta"-åtgärd. för steg 5 bör du använda en åtgärd "ta bort om det finns" i klientbiblioteket som du använder. Om du använder ett annat lagringssystem måste du använda en lämplig idempotent åtgärd.  

Om arbetarrollen aldrig slutför steg 6 i diagrammet visas meddelandet igen efter en timeout i kön som är redo för arbetarrollen att försöka bearbeta om den. Arbetarrollen kan kontrollera hur många gånger ett meddelande i kön har lästs och vid behov flagga det som ett "gift"-meddelande för undersökning genom att skicka det till en separat kö. Mer information om hur du läser kömeddelanden och kontrollerar antalet köer finns i [Hämta meddelanden](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Vissa fel från Tabelllagring och kölagring är tillfälliga fel och klientprogrammet bör innehålla lämplig logik för återförsök för att hantera dem.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Den här lösningen ger inte transaktionsisolering. En klient kan till exempel läsa tabellerna **Aktuellt** och **Arkivera** när arbetarrollen var mellan steg 4-5 i diagrammet och se en inkonsekvent vy av data. Uppgifterna kommer att vara konsekventa så småningom.  
* Du måste vara säker på att steg 4-5 är idempotenta för att säkerställa eventuell konsekvens.  
* Du kan skala lösningen med hjälp av flera köer och arbetsrollinstanser.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill garantera eventuell överensstämmelse mellan entiteter som finns i olika partitioner eller tabeller. Du kan utöka det här mönstret för att säkerställa eventuell konsekvens för åtgärder över tabelllagring och Blob-lagring och andra datakällor som inte är Azure Storage, till exempel en databas eller filsystemet.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Transaktioner i entitetsgrupp](#entity-group-transactions)  
* [Slå samman eller ersätta](#merge-or-replace)  

> [!NOTE]
> Om transaktionsisolering är viktigt för din lösning bör du överväga att omforma tabellerna så att du kan använda EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Mönster för indexentiteter
Underhåll indexentiteter för att möjliggöra effektiva sökningar som returnerar listor över entiteter.  

#### <a name="context-and-problem"></a>Kontext och problem
Tabelllagring indexerar automatiskt entiteter med hjälp av `PartitionKey` värdena och. `RowKey` Detta gör det möjligt för ett klientprogram att hämta en entitet effektivt med hjälp av en punktfråga. Med hjälp av följande tabellstruktur kan ett klientprogram till exempel effektivt hämta en enskild medarbetarentitet med hjälp av avdelningsnamnet och medarbetar-ID:t `PartitionKey` (och `RowKey`).  

![Grafik över medarbetarens entitet][13]

Om du också vill kunna hämta en lista över medarbetarentiteter baserat på värdet för en annan icke-unik egenskap, till exempel efternamn, måste du använda en mindre effektiv partitionsgenomsökning. Den här genomsökningen hittar matchningar i stället för att använda ett index för att slå upp dem direkt. Detta beror på att tabelllagring inte ger sekundära index.  

#### <a name="solution"></a>Lösning
Om du vill aktivera uppslag efter efternamn med föregående entitetsstruktur måste du underhålla listor över medarbetar-ID:n. Om du vill hämta de medarbetarentiteter som har ett visst efternamn, till exempel Jones, måste du först hitta listan över medarbetar-ID:n för medarbetare med Jones som efternamn och sedan hämta dessa medarbetarentiteter. Det finns tre huvudalternativ för att lagra listor över medarbetar-ID:  

* Använd Blob-lagring.  
* Skapa indexentiteter i samma partition som medarbetarentiteterna.  
* Skapa indexentiteter i en separat partition eller tabell.  

Alternativ 1: Använd Blob-lagring  

Skapa en blob för varje unikt efternamn och i varje `PartitionKey` blob `RowKey` lagra en lista över värdena (avdelning) och (medarbetar-ID) för medarbetare som har det efternamnet. När du lägger till eller tar bort en medarbetare ska du se till att innehållet i den relevanta blobben så småningom överensstämmer med medarbetarentiteterna.  

Alternativ 2: Skapa indexentiteter i samma partition  

Använd indexentiteter som lagrar följande data:  

![Grafik som visar medarbetarens entitet, med sträng som innehåller en lista över medarbetar-ID:n med samma efternamn][14]

Egenskapen `EmployeeIDs` innehåller en lista över medarbetar-ID:n `RowKey`för medarbetare med efternamnet lagrat i .  

I följande steg beskrivs den process du bör följa när du lägger till en ny medarbetare. I det här exemplet lägger vi till en medarbetare med ID 000152 och efternamn Jones på försäljningsavdelningen:  

1. Hämta indexentiteten `PartitionKey` med värdet "Försäljning" och `RowKey` värdet "Jones". Spara ETag för den här entiteten som ska användas i steg 2.  
2. Skapa en entitetsgruppstransaktion (det vill än`PartitionKey` en batchoperation) `RowKey` som infogar den nya medarbetarentiteten ( värdet "Försäljning" och värdet "000152" och uppdaterar indexentiteten (värdet`PartitionKey` "Försäljning" och `RowKey` värdet "Jones"). EGT gör detta genom att lägga till det nya medarbetar-ID:t i listan i fältet Employee IDs. Mer information om egts finns i [Transaktioner för entitetsgrupp](#entity-group-transactions).  
3. Om EGT misslyckas på grund av ett optimistiskt samtidighetsfel (det vill säga någon annan har ändrat indexentiteten) måste du börja om i steg 1.  

Du kan använda en liknande metod för att ta bort en medarbetare om du använder det andra alternativet. Att ändra en medarbetares efternamn är något mer komplext, eftersom du måste köra en EGT som uppdaterar tre entiteter: medarbetarentiteten, indexentiteten för det gamla efternamnet och indexentiteten för det nya efternamnet. Du måste hämta varje entitet innan du gör några ändringar, för att hämta ETag-värdena som du sedan kan använda för att utföra uppdateringarna med hjälp av optimistisk samtidighet.  

I följande steg beskrivs den process du bör följa när du behöver slå upp alla medarbetare med ett visst efternamn på en avdelning. I det här exemplet letar vi upp alla anställda med efternamn Jones på försäljningsavdelningen:  

1. Hämta indexentiteten `PartitionKey` med värdet "Försäljning" och `RowKey` värdet "Jones".  
2. Tolka listan över medarbetar-ID:er i fältet `EmployeeIDs` .  
3. Om du behöver ytterligare information om var och en av dessa medarbetare (till `PartitionKey` exempel deras e-postadresser), hämta var och en av medarbetarentiteterna med hjälp av värdet "Försäljning" och `RowKey` värden från listan över medarbetare som du erhöll i steg 2.  

Alternativ 3: Skapa indexentiteter i en separat partition eller tabell  

Använd indexentiteter som lagrar följande data för det här alternativet:  

![Grafik som visar medarbetarens entitet, med sträng som innehåller en lista över medarbetar-ID:n med samma efternamn][15]

Egenskapen `EmployeeIDs` innehåller en lista över medarbetar-ID:n `RowKey`för medarbetare med efternamnet lagrat i .  

Du kan inte använda EGT för att upprätthålla konsekvens, eftersom indexentiteterna finns i en separat partition från medarbetarentiteterna. Kontrollera att indexentiteterna så småningom överensstämmer med med de anställdas entiteter.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Den här lösningen kräver minst två frågor för att hämta matchande entiteter: en för att fråga indexentiteterna för att hämta listan över `RowKey` värden och sedan frågor för att hämta varje entitet i listan.  
* Eftersom en enskild entitet har en maximal storlek på 1 MB förutsätter alternativ 2 och alternativ 3 i lösningen att listan över medarbetar-ID:n för ett visst efternamn aldrig är mer än 1 MB. Om listan över medarbetar-ID:er sannolikt är mer än 1 MB stor använder du alternativ 1 och lagrar indexdata i Blob-lagring.  
* Om du använder alternativ 2 (med egts för att hantera att lägga till och ta bort medarbetare och ändra en medarbetares efternamn) måste du utvärdera om transaktionsvolymen närmar sig skalbarhetsgränserna i en viss partition. Om så är fallet bör du överväga en så småningom konsekvent lösning (alternativ 1 eller alternativ 3). Dessa använder köer för att hantera uppdateringsbegäranden och gör att du kan lagra indexentiteterna i en separat partition från medarbetarentiteterna.  
* Alternativ 2 i den här lösningen förutsätter att du vill slå upp efter efternamn inom en avdelning. Du vill till exempel hämta en lista över medarbetare med ett efternamn Jones på försäljningsavdelningen. Om du vill kunna slå upp alla anställda med ett efternamn Jones i hela organisationen, använd antingen alternativ 1 eller alternativ 3.
* Du kan implementera en köbaserad lösning som ger slutlig konsekvens. Mer information finns i [mönstret Så småningom konsekventa transaktioner](#eventually-consistent-transactions-pattern).  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill slå upp en uppsättning entiteter som alla delar ett gemensamt egenskapsvärde, till exempel alla medarbetare med efternamnet Jones.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Sammansattnyckelmönster](#compound-key-pattern)  
* [Så småningom konsekvent transaktionsmönster](#eventually-consistent-transactions-pattern)  
* [Transaktioner i entitetsgrupp](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Denormalisering mönster
Kombinera relaterade data tillsammans i en enda entitet så att du kan hämta alla data du behöver med en enda punktfråga.  

#### <a name="context-and-problem"></a>Kontext och problem
I en relationsdatabas normaliserar du vanligtvis data för att ta bort duplicering som uppstår när frågor hämtar data från flera tabeller. Om du normaliserar dina data i Azure-tabeller måste du göra flera tur- och returresor från klienten till servern för att hämta relaterade data. Med följande tabellstruktur behöver du till exempel två tur- och returresor för att hämta information för en avdelning. En resa hämtar den avdelningsenhet som innehåller chefens ID och den andra resan hämtar chefens information i en medarbetarentitet.  

![Grafik för avdelningens entitet och medarbetarenhet][16]

#### <a name="solution"></a>Lösning
I stället för att lagra data i två separata entiteter, denormalisera data och behålla en kopia av chefens information i avdelningentiteten. Ett exempel:  

![Grafik av denormaliserad och kombinerad avdelningsenhet][17]

Med avdelningsentiteter lagrade med dessa egenskaper kan du nu hämta all information du behöver om en avdelning med hjälp av en punktfråga.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Det finns vissa kostnader i samband med lagring av vissa data två gånger. Prestandavinsten till följd av färre begäranden till tabelllagring uppväger vanligtvis den marginella ökningen av lagringskostnaderna. Vidare kompenseras den här kostnaden delvis av en minskning av antalet transaktioner som du behöver för att hämta information om en avdelning.  
* Du måste behålla konsekvensen i de två entiteter som lagrar information om chefer. Du kan hantera konsekvensproblemet genom att använda EGTs för att uppdatera flera entiteter i en enda atomär transaktion. I det här fallet lagras avdelningsentiteten och medarbetarentiteten för avdelningschefen i samma partition.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du ofta behöver söka efter relaterad information. Det här mönstret minskar antalet frågor som klienten måste ställa för att hämta de data som krävs.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Sammansattnyckelmönster](#compound-key-pattern)  
* [Transaktioner i entitetsgrupp](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Sammansattnyckelmönster
Använd `RowKey` sammansatta värden för att en klient ska kunna slå upp relaterade data med en enda punktfråga.  

#### <a name="context-and-problem"></a>Kontext och problem
I en relationsdatabas är det naturligt att använda kopplingar i frågor för att returnera relaterade datadelar till klienten i en enda fråga. Du kan till exempel använda medarbetar-ID för att slå upp en lista över relaterade entiteter som innehåller prestanda- och granskningsdata för medarbetaren.  

Anta att du lagrar medarbetarentiteter i Tabelllagring med hjälp av följande struktur:  

![Grafik över medarbetarens entitet][18]

Du måste också lagra historiska data om recensioner och resultat för varje år som medarbetaren har arbetat för din organisation, och du måste kunna komma åt den här informationen per år. Ett alternativ är att skapa en annan tabell som lagrar entiteter med följande struktur:  

![Grafik över entiteten för personalgranskning][19]

Med den här metoden kan du välja att duplicera viss information (till exempel förnamn och efternamn) i den nya entiteten, så att du kan hämta dina data med en enda begäran. Du kan dock inte upprätthålla en stark konsekvens eftersom du inte kan använda en EGT för att uppdatera de två entiteterna atomärt.  

#### <a name="solution"></a>Lösning
Lagra en ny entitetstyp i den ursprungliga tabellen med hjälp av entiteter med följande struktur:  

![Grafik av medarbetarens entitet med sammansatt nyckel][20]

Lägg märke `RowKey` till hur det nu är en sammansatt nyckel, som består av medarbetar-ID och året för granskningsdata. På så sätt kan du hämta medarbetarens prestanda och granska data med en enda begäran om en enda entitet.  

I följande exempel beskrivs hur du kan hämta alla granskningsdata för en viss medarbetare (till exempel medarbetare 000123 på försäljningsavdelningen):  

$filter=(PartitionKey eq 'Sales') och (RowKey ge 'empid_000123') och (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du bör använda ett lämpligt avgränsare som gör `RowKey` det enkelt att tolka värdet: till exempel **000123_2012**.  
* Du lagrar också den här entiteten i samma partition som andra entiteter som innehåller relaterade data för samma medarbetare. Detta innebär att du kan använda EGTs för att upprätthålla en stark konsekvens.
* Du bör överväga hur ofta du frågar data för att avgöra om det här mönstret är lämpligt. Om du till exempel använder granskningsdata sällan och de viktigaste medarbetardata ofta, bör du behålla dem som separata entiteter.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra en eller flera relaterade entiteter som du frågar ofta.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Transaktioner i entitetsgrupp](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#work-with-heterogeneous-entity-types)  
* [Så småningom konsekvent transaktionsmönster](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Mönster för stocksvans
Hämta *n* n-entiteterna som senast lades `RowKey` till i en partition med hjälp av ett värde som sorterar i omvänd datum och tidsordning.  

> [!NOTE]
> Frågeresultat som returneras av Azure Table API i Azure Cosmos DB sorteras inte efter partitionsnyckel eller radnyckel. Det här mönstret är därför lämpligt för tabelllagring, men det är inte lämpligt för Azure Cosmos DB. En detaljerad lista över funktionsskillnader finns [i skillnader mellan tabell-API i Azure Cosmos DB och Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

#### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt krav är att kunna hämta de senast skapade entiteterna, till exempel de tio senaste utgiftsanspråk som lämnats in av en medarbetare. Tabellfrågor stöder `$top` en frågeåtgärd för att returnera de första *n entiteterna* från en uppsättning. Det finns ingen motsvarande frågeåtgärd för att returnera de sista *n entiteterna* i en uppsättning.  

#### <a name="solution"></a>Lösning
Lagra entiteterna `RowKey` med hjälp av en som naturligt sorterar i omvänd datum/tidsordning, så den senaste posten är alltid den första i tabellen.  

Om du till exempel vill kunna hämta de tio senaste utgiftsanspråk som skickats av en medarbetare kan du använda ett omvänt skalstreck som härletts från aktuellt datum/tid. Följande C#-kodexempel visar ett sätt att skapa ett lämpligt `RowKey` "inverterade fästingar" värde för en som sorterar från den senaste till den äldsta:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Du kan återgå till datum-/tidsvärdet med hjälp av följande kod:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Tabellfrågan ser ut så här:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du måste pad omvänd skalstreck värde med inledande nollor, för att säkerställa strängen värde sorterar som förväntat.  
* Du måste vara medveten om skalbarhetsmålen på partitionsnivå. Var noga med att inte skapa hot spot-partitioner.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver komma åt entiteter i omvänd datum/tidsordning eller när du behöver komma åt de senast tillagda entiteterna.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Prepend / bifoga anti-mönster](#prepend-append-anti-pattern)  
* [Hämta entiteter](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Ta bort mönster för hög volym
Aktivera borttagning av en stor mängd entiteter genom att lagra alla entiteter för samtidig borttagning i sin egen separata tabell. Du tar bort entiteterna genom att ta bort tabellen.  

#### <a name="context-and-problem"></a>Kontext och problem
Många program tar bort gamla data som inte längre behöver vara tillgängliga för ett klientprogram eller som programmet har arkiverat till ett annat lagringsmedium. Du identifierar vanligtvis sådana data efter ett datum. Du har till exempel ett krav på att ta bort poster för alla inloggningsbegäranden som är äldre än 60 dagar.  

En möjlig design är att använda datum och tid `RowKey`för inloggningsbegäran i :  

![Bild av entiteten inloggningsförsök][21]

Den här metoden undviker partitionshotspots, eftersom programmet kan infoga och ta bort inloggningsenheter för varje användare i en separat partition. Den här metoden kan dock vara kostsam och tidskrävande om du har ett stort antal enheter. Först måste du utföra en tabellsökning för att identifiera alla entiteter som ska tas bort, och sedan måste du ta bort varje gammal entitet. Du kan minska antalet tur- och returresor till den server som krävs för att ta bort de gamla entiteterna genom att batcha flera borttagningsbegäranden i EGTs.  

#### <a name="solution"></a>Lösning
Använd en separat tabell för varje dag med inloggningsförsök. Du kan använda föregående entitetsdesign för att undvika aktiveringspunkter när du infogar entiteter. Att ta bort gamla entiteter är nu helt enkelt en fråga om att ta bort en tabell varje dag (en enda lagringsåtgärd), i stället för att hitta och ta bort hundratals och tusentals enskilda inloggningsenheter varje dag.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Stöder din design andra sätt som ditt program använder data på, till exempel att leta upp specifika entiteter, länka med andra data eller generera aggregerad information?  
* Undviker din design aktiva punkter när du infogar nya enheter?  
* Räkna med en fördröjning om du vill återanvända samma tabellnamn när du har tagit bort det. Det är bättre att alltid använda unika tabellnamn.  
* Förvänta dig en viss hastighetsbegränsning när du först använder en ny tabell, medan tabelllagring lär sig åtkomstmönstren och distribuerar partitionerna över noder. Du bör överväga hur ofta du behöver skapa nya tabeller.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du har en hög mängd entiteter som du måste ta bort samtidigt.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Transaktioner i entitetsgrupp](#entity-group-transactions)
* [Ändrar entiteter](#modify-entities)  

### <a name="data-series-pattern"></a>Mönster för dataserier
Lagra fullständiga dataserier i en enda entitet för att minimera antalet begäranden du gör.  

#### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt scenario är att ett program lagrar en serie data som det vanligtvis behöver hämta alla på en gång. Ditt program kan till exempel registrera hur många snabbmeddelande som varje anställd skickar varje timme och sedan använda den här informationen för att rita hur många meddelanden varje användare har skickat under de föregående 24 timmarna. En design kan vara att lagra 24 entiteter för varje anställd:  

![Bild av entiteten meddelandestatistik][22]

Med den här designen kan du enkelt hitta och uppdatera entiteten för att uppdatera för varje medarbetare när programmet behöver uppdatera värdet för antalet meddelanden. Om du vill hämta informationen för att rita ett diagram över aktiviteten under de föregående 24 timmarna måste du hämta 24 entiteter.  

#### <a name="solution"></a>Lösning
Använd följande design, med en separat egenskap för att lagra antalet meddelanden för varje timme:  

![Bild som visar entiteten meddelandestatistik med separerade egenskaper][23]

Med den här designen kan du använda en kopplingsåtgärd för att uppdatera meddelandeantalet för en medarbetare under en viss timme. Nu kan du hämta all information du behöver för att rita diagrammet med hjälp av en begäran om en enda entitet.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Om hela dataserien inte passar in i en enda entitet (en entitet kan ha upp till 252 egenskaper) använder du ett alternativt datalager, till exempel en blob.  
* Om du har flera klienter som uppdaterar en entitet samtidigt använder du **ETag för** att implementera optimistisk samtidighet. Om du har många kunder, kan du uppleva hög konkurrens.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver uppdatera och hämta en dataserie som är associerad med en enskild entitet.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Mönster för stora enheter](#large-entities-pattern)  
* [Slå samman eller ersätta](#merge-or-replace)  
* [Så småningom konsekvent transaktionsmönster](#eventually-consistent-transactions-pattern) (om du lagrar dataserien i en blob)  

### <a name="wide-entities-pattern"></a>Brett entitetsmönster
Använd flera fysiska entiteter för att lagra logiska entiteter med mer än 252 egenskaper.  

#### <a name="context-and-problem"></a>Kontext och problem
En enskild entitet kan inte ha fler än 252 egenskaper (exklusive obligatoriska systemegenskaper) och kan inte lagra mer än 1 MB data totalt. I en relationsdatabas kan du vanligtvis undvika alla gränser för storleken på en rad genom att lägga till en ny tabell och tillämpa en 1-till-1-relation mellan dem.  

#### <a name="solution"></a>Lösning
Genom att använda Tabelllagring kan du lagra flera entiteter för att representera ett enda stort affärsobjekt med fler än 252 egenskaper. Om du till exempel vill lagra antalet snabbmeddelandemeddelanden som skickats av varje medarbetare under de senaste 365 dagarna kan du använda följande design som använder två entiteter med olika scheman:  

![Bild som visar entiteten meddelandestatistik med Entiteten Rowkey 01 och meddelandestatistik med Rowkey 02][24]

Om du behöver göra en ändring som kräver att du uppdaterar båda entiteterna för att hålla dem synkroniserade med varandra kan du använda en EGT. Annars kan du använda en enda kopplingsåtgärd för att uppdatera antalet meddelanden för en viss dag. Om du vill hämta alla data för en enskild medarbetare måste du hämta båda entiteterna. Du kan göra detta med två `PartitionKey` effektiva `RowKey` begäranden som använder både ett och ett värde.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera det här mönstret:  

* Hämtning av en fullständig logisk entitet omfattar minst två lagringstransaktioner: en för att hämta varje fysisk entitet.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra entiteter vars storlek eller antal egenskaper överskrider gränserna för en enskild entitet i Tabelllagring.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Transaktioner i entitetsgrupp](#entity-group-transactions)
* [Slå samman eller ersätta](#merge-or-replace)

### <a name="large-entities-pattern"></a>Mönster för stora enheter
Använd Blob-lagring för att lagra stora egenskapsvärden.  

#### <a name="context-and-problem"></a>Kontext och problem
En enskild entitet kan inte lagra mer än 1 MB data totalt. Om ett eller flera av dina egenskapslagringsvärden som gör att den totala storleken på din entitet överskrider det här värdet kan du inte lagra hela entiteten i Tabelllagring.  

#### <a name="solution"></a>Lösning
Om din entitet är större än 1 MB eftersom en eller flera egenskaper innehåller en stor mängd data, kan du lagra data i Blob-lagring och sedan lagra adressen till bloben i en egenskap i entiteten. Du kan till exempel lagra fotot på en anställd i Blob-lagring `Photo` och lagra en länk till fotot i egenskapen för din medarbetarentitet:  

![Bild som visar medarbetarens entitet med sträng för Foto som pekar på Blob-lagring][25]

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Om du vill behålla eventuell överensstämmelse mellan entiteten i tabelllagring och data i Blob-lagring använder du [mönstret Så småningom konsekventa transaktioner](#eventually-consistent-transactions-pattern) för att underhålla dina entiteter.
* Hämtning av en fullständig entitet omfattar minst två lagringstransaktioner: en för att hämta entiteten och en för att hämta blob-data.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra entiteter vars storlek överskrider gränserna för en enskild entitet i Tabelllagring.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Så småningom konsekvent transaktionsmönster](#eventually-consistent-transactions-pattern)  
* [Brett entitetsmönster](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Prepend/lägg till anti-mönster
När du har en hög mängd skär ökar du skalbarheten genom att sprida infogningen över flera partitioner.  

#### <a name="context-and-problem"></a>Kontext och problem
Prepending eller lägga till entiteter till dina lagrade entiteter resulterar vanligtvis i att programmet lägger till nya entiteter i den första eller sista partitionen i en sekvens av partitioner. I det här fallet sker alla skär vid en viss tidpunkt i samma partition, vilket skapar en aktiveringspunkt. Detta förhindrar att tabelllagring inte är belastningsutjämningsinsatser över flera noder och eventuellt gör att programmet träffar skalbarhetsmålen för partitionen. Tänk till exempel på fallet med ett program som loggar nätverks- och resursåtkomst av medarbetare. En entitetsstruktur som följande kan resultera i att den aktuella timmens partition blir en aktiveringspunkt, om transaktionsvolymen når skalbarhetsmålet för en enskild partition:  

![Grafik över medarbetarens entitet][26]

#### <a name="solution"></a>Lösning
Följande alternativa entitetsstruktur undviker en aktiveringspunkt på en viss partition, eftersom programmet loggar händelser:  

![Bild som visar medarbetarens entitet med RowKey-kompoundering av id-programmet År, Månad, Dag, Timme och Händelse][27]

Lägg märke till med `PartitionKey` `RowKey` det här exemplet hur både och är sammansatta nycklar. Använder `PartitionKey` både avdelning och medarbetar-ID för att distribuera loggningen över flera partitioner.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Stöder den alternativa nyckelstrukturen som undviker att skapa heta partitioner på skär effektivt de frågor som klientprogrammet ställer?  
* Innebär den förväntade transaktionsvolymen att du sannolikt kommer att nå skalbarhetsmålen för en enskild partition och begränsas av tabelllagring?  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Undvik det anti-mönster som är förpresend/tillägg när transaktionsvolymen sannolikt kommer att resultera i hastighetsbegränsning med tabelllagring när du öppnar en frekvent partition.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Sammansattnyckelmönster](#compound-key-pattern)  
* [Mönster för stocksvans](#log-tail-pattern)  
* [Ändrar entiteter](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Logga data mot mönster
Vanligtvis bör du använda Blob-lagring i stället för Tabelllagring för att lagra loggdata.  

#### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt användningsfall för loggdata är att hämta ett urval av loggposter för ett visst datum-/tidsintervall. Du vill till exempel hitta alla felmeddelanden och viktiga meddelanden som programmet loggade mellan 15:04 och 15:06 på ett visst datum. Du vill inte använda datum och tid för loggmeddelandet för att avgöra vilken partition du sparar loggentiteter till. Det resulterar i en het partition eftersom alla loggentiteter vid en viss tidpunkt kommer att dela samma `PartitionKey` värde (se [antimönstret Prepend/append](#prepend-append-anti-pattern)). Följande entitetsschema för ett loggmeddelande resulterar till exempel i en frekvent partition, eftersom programmet skriver alla loggmeddelanden till partitionen för aktuellt datum och timme:  

![Bild av loggmeddelandeentiteten][28]

I det här `RowKey` exemplet innehåller datum och tid för loggmeddelandet för att säkerställa att loggmeddelanden sorteras i datum/tidsordning. Den `RowKey` innehåller också ett meddelande-ID, om flera loggmeddelanden delar samma datum och tid.  

En annan metod `PartitionKey` är att använda en som säkerställer att programmet skriver meddelanden över en rad partitioner. Om källan till loggmeddelandet till exempel är ett sätt att distribuera meddelanden över många partitioner kan du använda följande entitetsschema:  

![Bild av loggmeddelandeentiteten][29]

Problemet med det här schemat är dock att om du vill hämta alla loggmeddelanden under en viss tidsperiod måste du söka igenom alla partitioner i tabellen.

#### <a name="solution"></a>Lösning
I föregående avsnitt markeras problemet med att försöka använda Tabelllagring för att lagra loggposter och föreslog två otillfredsställande designer. En lösning ledde till en het partition med risk för dålig prestanda att skriva loggmeddelanden. Den andra lösningen resulterade i dåliga frågeprestanda, på grund av kravet på att söka igenom varje partition i tabellen för att hämta loggmeddelanden under en viss tidsperiod. Blob storage erbjuder en bättre lösning för den här typen av scenario, och det är så Azure Storage-analys lagrar loggdata som samlas in.  

I det här avsnittet beskrivs hur lagringsanalys lagrar loggdata i Blob-lagring, som en illustration av den här metoden för att lagra data som du vanligtvis frågar efter intervall.  

Lagringsanalys lagrar loggmeddelanden i avgränsat format i flera blobbar. Det avgränsade formatet gör det enkelt för ett klientprogram att tolka data i loggmeddelandet.  

Lagringsanalys använder en namngivningskonvention för blobbar som gör att du kan hitta blobben (eller blobbar) som innehåller de loggmeddelanden som du söker efter. Till exempel innehåller en blob med namnet "queue/2014/07/31/1800/000001.log" loggmeddelanden som relaterar till kötjänsten för timmen som börjar klockan 18:00 den 31 juli 2014. "000001" anger att detta är den första loggfilen för den här perioden. Lagringsanalys registrerar också tidsstämplarna för de första och sista loggmeddelanden som lagras i filen, som en del av blobens metadata. API:et för Blob-lagring gör att du kan hitta blobbar i en behållare baserat på ett namnprefix. Om du vill hitta alla blobbar som innehåller köloggdata för timmen som börjar klockan 18:00 kan du använda prefixet "queue/2014/07/31/1800".  

Lagringsanalys buffrar loggmeddelanden internt och uppdaterar sedan regelbundet lämplig blob eller skapar en ny med den senaste batchen med loggposter. Detta minskar antalet skrivningar som den måste utföra till Blob-lagring.  

Om du implementerar en liknande lösning i ditt eget program kan du överväga hur du hanterar avvägningen mellan tillförlitlighet och kostnad och skalbarhet. Med andra ord utvärdera effekten av att skriva varje loggpost till Blob-lagring när det händer, jämfört med buffring av uppdateringar i ditt program och skriva dem till Blob-lagring i batchar.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande punkter när du bestämmer hur loggdata ska lagras:  

* Om du skapar en tabelldesign som undviker potentiella heta partitioner kan det hända att du inte kan komma åt loggdata på ett effektivt sätt.  
* För att bearbeta loggdata måste en klient ofta läsa in många poster.  
* Även om loggdata ofta är strukturerade kan Blob-lagring vara en bättre lösning.  

### <a name="implementation-considerations"></a>Att tänka på vid implementering
I det här avsnittet beskrivs några av de överväganden som ska beaktas när du implementerar de mönster som beskrivs i föregående avsnitt. De flesta av de här avsnitten använder exempel skrivna i C# som använder Storage Client Library (version 4.3.0 i skrivande stund).  

### <a name="retrieve-entities"></a>Hämta entiteter
Som beskrivs i avsnittet [Design för frågor](#design-for-querying)är den mest effektiva frågan en punktfråga. I vissa fall kan du dock behöva hämta flera entiteter. I det här avsnittet beskrivs några vanliga metoder för att hämta entiteter med hjälp av storage client-biblioteket.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Köra en punktfråga med hjälp av lagringsklientbiblioteket
Det enklaste sättet att köra en punktfråga är att använda åtgärden **Hämta** tabell. Som visas i följande C#-kodavsnitt hämtar den här `PartitionKey` åtgärden en entitet `RowKey` med värdet "Försäljning" och värdet "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Lägg märke till hur det här exemplet `EmployeeEntity`förväntar sig att entiteten som hämtas ska vara av typen .  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Hämta flera entiteter med hjälp av LINQ
Du kan hämta flera entiteter genom att använda LINQ med Storage Client Library och ange en fråga med en **where-sats.** För att undvika en tabellsökning `PartitionKey` bör du alltid inkludera värdet `RowKey` i where-satsen och om möjligt värdet för att undvika tabell- och partitionssökningar. Tabelllagring stöder en begränsad uppsättning jämförelseoperatorer (större än, större än eller lika, mindre än, mindre än eller lika, lika och inte lika) att använda i where-satsen. Följande C#-kodavsnitt hittar alla anställda vars efternamn börjar med "B" (förutsatt att efternamnet `RowKey` lagrars) `PartitionKey` i försäljningsavdelningen (förutsatt att varuhusnamnet lagras):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Lägg märke till hur `RowKey` frågan `PartitionKey` anger både a och a för att säkerställa bättre prestanda.  

Följande kodexempel visar likvärdiga funktioner med hjälp av det flytande API:et (mer information om flytande API:er i allmänhet finns i [Metodtips för att utforma ett flytande API):](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)  

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
> Exemplet kapslar `CombineFilters` flera metoder för att inkludera de tre filtervillkoren.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Hämta ett stort antal entiteter från en fråga
En optimal fråga returnerar en `PartitionKey` enskild `RowKey` entitet baserat på ett värde och ett värde. I vissa fall kan du dock ha ett krav på att returnera många entiteter från samma partition, eller till och med från många partitioner. Du bör alltid testa programmets prestanda i sådana scenarier.  

En fråga mot tabelllagring kan returnera högst 1 000 entiteter samtidigt och köras i högst fem sekunder. Tabelllagring returnerar en fortsättningstoken för att klientprogrammet ska kunna begära nästa uppsättning entiteter, om något av följande är sant:

- Resultatuppsättningen innehåller mer än 1 000 entiteter.
- Frågan slutfördes inte inom fem sekunder.
- Frågan korsar partitionsgränsen. 

Mer information om hur fortsättningstoken fungerar finns i [Fråga timeout och sidnumrering](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Om du använder lagringsklientbiblioteket kan det automatiskt hantera fortsättningstoken åt dig när det returnerar entiteter från tabelllagring. Följande C#-kodexempel hanterar till exempel automatiskt fortsättningstoken om tabelllagring returnerar dem i ett svar:  

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

Följande C#-kod hanterar fortsättningstoken uttryckligen:  

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

Genom att uttryckligen använda fortsättningstoken kan du styra när programmet hämtar nästa datasegment. Om klientprogrammet till exempel gör det möjligt för användare att bläddra igenom de entiteter som lagras i en tabell, kan en användare välja att inte bläddra igenom alla entiteter som hämtas av frågan. Ditt program skulle bara använda en fortsättningstoken för att hämta nästa segment när användaren hade slutfört växlingen genom alla entiteter i det aktuella segmentet. Detta tillvägagångssätt har flera fördelar:  

* Du kan begränsa mängden data som ska hämtas från Tabelllagring och som du flyttar över nätverket.  
* Du kan utföra asynkron I/O i .NET.  
* Du kan serialisera fortsättningstoken till beständig lagring, så att du kan fortsätta i händelse av en programkrasch.  

> [!NOTE]
> En fortsättningstoken returnerar vanligtvis ett segment som innehåller 1 000 entiteter, även om det kan innehålla färre. Detta är också fallet om du begränsar antalet poster som en fråga returnerar genom att använda **Ta** för att returnera de första n-entiteterna som matchar uppslagsvillkoren. Tabelllagring kan returnera ett segment som innehåller färre än n entiteter, tillsammans med en fortsättningstoken så att du kan hämta de återstående entiteterna.  
> 
> 

Följande C#-kod visar hur du ändrar antalet entiteter som returneras inom ett segment:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projektion på serversidan
En enda entitet kan ha upp till 255 egenskaper och vara upp till 1 MB i storlek. När du frågar tabellen och hämtar entiteter kanske du inte behöver alla egenskaper och kan undvika att överföra data i onödan (för att minska svarstiden och kostnaden). Du kan använda projektion på serversidan för att överföra bara de egenskaper du behöver. I följande exempel hämtas bara `Email` `PartitionKey`egenskapen (tillsammans med , `RowKey`, `Timestamp`och `ETag`) från de entiteter som valts av frågan.  

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

Lägg märke `RowKey` till hur värdet är tillgängligt även om det inte ingår i listan över egenskaper att hämta.  

### <a name="modify-entities"></a>Ändrar entiteter
Med storage client-biblioteket kan du ändra entiteterna som lagras i Tabelllagring genom att infoga, ta bort och uppdatera entiteter. Du kan använda EGTs för att batcha flera skär, uppdatera och ta bort åtgärder tillsammans, för att minska antalet tur- och returresor som krävs och förbättra prestanda för din lösning.  

Undantag som genereras när storage client library kör en EGT innehåller vanligtvis index för entiteten som orsakade batchen misslyckas. Detta är användbart när du felsöker kod som använder EGTs.  

Du bör också överväga hur din design påverkar hur klientprogrammet hanterar samtidighet och uppdateringsåtgärder.  

#### <a name="managing-concurrency"></a>Hantera samtidighet
Som standard implementerar tabelllagring optimistiska samtidighetskontroller på nivån för enskilda entiteter för att infoga, sammanfoga och ta bort åtgärder, även om det är möjligt för en klient att tvinga tabelllagring att kringgå dessa kontroller. Mer information finns [i Hantera samtidighet i Microsoft Azure Storage](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Slå samman eller ersätta
Metoden `Replace` för `TableOperation` klassen ersätter alltid den fullständiga entiteten i Tabelllagring. Om du inte inkluderar en egenskap i begäran när den egenskapen finns i den lagrade entiteten, tas den egenskapen bort från den lagrade entiteten. Om du inte vill ta bort en egenskap uttryckligen från en lagrad entitet måste du inkludera alla egenskaper i begäran.  

Du kan `Merge` använda klassens `TableOperation` metod för att minska mängden data som du skickar till Tabelllagring när du vill uppdatera en entitet. Metoden `Merge` ersätter alla egenskaper i den lagrade entiteten med egenskapsvärden från den entitet som ingår i begäran. Den här metoden lämnar intakta egenskaper i den lagrade entiteten som inte ingår i begäran. Detta är användbart om du har stora entiteter och bara behöver uppdatera ett litet antal egenskaper i en begäran.  

> [!NOTE]
> `*Replace` Metoderna `Merge` och misslyckas om entiteten inte finns. Som ett alternativ kan `InsertOrReplace` du `InsertOrMerge` använda metoderna och metoderna som skapar en ny entitet om den inte finns.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Arbeta med heterogena entitetstyper
Tabelllagring är ett *schemalösa* tabellarkiv. Det innebär att en enda tabell kan lagra entiteter av flera typer, vilket ger stor flexibilitet i din design. I följande exempel visas en tabell som lagrar både medarbetare och avdelningsenheter:  

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
<th>DepartmentName</th>
<th>MedarbetareCount</th>
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

Varje entitet `PartitionKey` `RowKey`måste `Timestamp` fortfarande ha , och värden, men kan ha alla uppsättning egenskaper. Dessutom finns det inget som anger vilken typ av en entitet om du inte väljer att lagra den informationen någonstans. Det finns två alternativ för att identifiera entitetstypen:  

* Förbered entitetstypen `RowKey` till (eller `PartitionKey`möjligen ). Till `EMPLOYEE_000123` exempel, `DEPARTMENT_SALES` `RowKey` eller som värden.  
* Använd en separat egenskap för att registrera entitetstypen, som visas i följande tabell.  

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
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Anställd</td>
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
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Anställd</td>
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
<th>DepartmentName</th>
<th>MedarbetareCount</th>
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
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Anställd</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Det första alternativet, som väntar `RowKey`på entitetstypen till , är användbart om det finns en möjlighet att två entiteter av olika typer kan ha samma nyckelvärde. Den grupperar också entiteter av samma typ tillsammans i partitionen.  

De tekniker som beskrivs i det här avsnittet är särskilt relevanta för diskussionen om[arvsrelationer](#inheritance-relationships).  

> [!NOTE]
> Överväg att inkludera ett versionsnummer i värdet för entitetstyp, så att klientprogram kan utveckla POCO-objekt och arbeta med olika versioner.  
> 
> 

I resten av det här avsnittet beskrivs några av funktionerna i lagringsklientbiblioteket som underlättar arbetet med flera entitetstyper i samma tabell.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Hämta typer av heterogena entiteter
Om du använder lagringsklientbiblioteket har du tre alternativ för att arbeta med flera entitetstyper.  

Om du känner till typen `RowKey` av `PartitionKey` entitet som lagras med specifika värden och värden kan du ange entitetstypen när du hämtar entiteten. Du såg detta i de två föregående `EmployeeEntity`exemplen som hämtar entiteter av typen : [Kör en punktfråga med hjälp av Storage Client Library](#run-a-point-query-by-using-the-storage-client-library) och Hämta flera [entiteter med hjälp av LINQ](#retrieve-multiple-entities-by-using-linq).  

Det andra alternativet är `DynamicTableEntity` att använda typen (en egenskapspåse) i stället för en konkret POCO-entitetstyp. Det här alternativet kan också förbättra prestanda, eftersom det inte finns något behov av att serialisera och avserialisera entiteten till .NET-typer. Följande C#-kod hämtar potentiellt flera entiteter av olika typer `DynamicTableEntity` från tabellen, men returnerar alla entiteter som instanser. Den använder `EntityType` sedan egenskapen för att bestämma vilken typ av varje entitet:  

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

Om du vill hämta andra `TryGetValue` egenskaper `Properties` måste du `DynamicTableEntity` använda metoden för klassens egenskap.  

Ett tredje alternativ är `DynamicTableEntity` att kombinera `EntityResolver` med typen och en instans. På så sätt kan du matcha till flera POCO-typer i samma fråga. I det här `EntityResolver` exemplet använder `EntityType` ombudet egenskapen för att skilja mellan de två typer av entiteter som frågan returnerar. Metoden `Resolve` använder `resolver` ombudet `DynamicTableEntity` för att `TableEntity` matcha instanser till instanser.  

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

#### <a name="modify-heterogeneous-entity-types"></a>Ändra typer av heterogena entiteter
Du behöver inte känna till vilken typ av en entitet som ska tas bort, och du vet alltid vilken typ av en entitet när du infogar den. Du kan dock `DynamicTableEntity` använda typen för att uppdatera en entitet utan att känna till dess typ och utan att använda en POCO-entitetsklass. Följande kodexempel hämtar en enda entitet och kontrollerar att egenskapen finns innan den `EmployeeCount` uppdateras.  

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

### <a name="control-access-with-shared-access-signatures"></a>Kontrollera åtkomst med signaturer för delad åtkomst
Du kan använda SAS-token (Shared Access Signature) för att klientprogram ska kunna ändra (och fråga) tabellentiteter direkt, utan att behöva autentisera direkt med tabelllagring. Vanligtvis finns det tre huvudsakliga fördelar med att använda SAS i ditt program:  

* Du behöver inte distribuera din lagringskontonyckel till en osäker plattform (till exempel en mobil enhet) för att enheten ska kunna komma åt och ändra entiteter i Tabelllagring.  
* Du kan avlasta en del av det arbete som webb- och arbetsroller utför för att hantera dina entiteter. Du kan avlasta till klientenheter som slutanvändardatorer och mobila enheter.  
* Du kan tilldela en klient en klient en begränsad och tidsbegränsad uppsättning behörigheter (t.ex. tillåta skrivskyddad åtkomst till specifika resurser).  

Mer information om hur du använder SAS-token med Tabelllagring finns i [Använda SIGNATURER för delad åtkomst (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Du måste dock fortfarande generera SAS-token som ger ett klientprogram till entiteterna i tabelllagring. Gör detta i en miljö som har säker åtkomst till dina lagringskontonycklar. Vanligtvis använder du en webb- eller arbetarroll för att generera SAS-token och leverera dem till klientprogrammen som behöver åtkomst till dina entiteter. Eftersom det fortfarande finns en overhead som är involverad i att generera och leverera SAS-token till klienter, bör du överväga hur du bäst kan minska den här omkostnaden, särskilt i scenarier med stora volymer.  

Det är möjligt att generera en SAS-token som ger åtkomst till en delmängd av entiteterna i en tabell. Som standard skapar du en SAS-token för en hel tabell. Men det är också möjligt att ange att SAS-token `PartitionKey` beviljar åtkomst `PartitionKey` till `RowKey` antingen ett intervall av värden eller ett intervall av och värden. Du kan välja att generera SAS-token för enskilda användare av ditt system, så att varje användares SAS-token endast ger dem åtkomst till sina egna entiteter i Tabelllagring.  

### <a name="asynchronous-and-parallel-operations"></a>Asynkrona och parallella operationer
Förutsatt att du sprider dina begäranden över flera partitioner kan du förbättra dataflödet och klientens svarstider med hjälp av asynkrona eller parallella frågor.
Du kan till exempel ha två eller flera arbetsrollinstanser som ansluter till tabellerna parallellt. Du kan ha enskilda arbetsroller som ansvarar för vissa uppsättningar partitioner, eller helt enkelt ha flera arbetsrollinstanser, som var och en kan komma åt alla partitioner i en tabell.  

Inom en klientinstans kan du förbättra dataflödet genom att köra lagringsåtgärder asynkront. Storage Client Library gör det enkelt att skriva asynkrona frågor och ändringar. Du kan till exempel börja med den synkrona metoden som hämtar alla entiteter i en partition, vilket visas i följande C#-kod:  

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

Du kan enkelt ändra den här koden så att frågan körs asynkront, enligt följande:  

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

I det här asynkrona exemplet kan du se följande ändringar från den synkrona versionen:  

* Metodsignaturen `async` innehåller nu modifieraren och returnerar en `Task` instans.  
* I stället `ExecuteSegmented` för att anropa metoden för `ExecuteSegmentedAsync` att hämta resultat anropar metoden nu metoden. Metoden använder `await` modifieraren för att hämta resultat asynkront.  

Klientprogrammet kan anropa den här metoden flera `department` gånger, med olika värden för parametern. Varje fråga körs på en separat tråd.  

Det finns ingen asynkron `Execute` version av `TableQuery` metoden i `IEnumerable` klassen, eftersom gränssnittet inte stöder asynkron uppräkning.  

Du kan också infoga, uppdatera och ta bort entiteter asynkront. I följande C#-exempel visas en enkel, synkron metod för att infoga eller ersätta en medarbetaretitet:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Du kan enkelt ändra den här koden så att uppdateringen körs asynkront, enligt följande:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

I det här asynkrona exemplet kan du se följande ändringar från den synkrona versionen:  

* Metodsignaturen `async` innehåller nu modifieraren och returnerar en `Task` instans.  
* I stället `Execute` för att anropa metoden för att `ExecuteAsync` uppdatera entiteten anropar metoden nu metoden. Metoden använder `await` modifieraren för att hämta resultat asynkront.  

Klientprogrammet kan anropa flera asynkrona metoder som den här, och varje metodanrop körs på en separat tråd.  


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


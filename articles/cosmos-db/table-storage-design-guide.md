---
title: Design Azure Cosmos DB tabeller för skalning och prestanda
description: 'Design Guide för Azure Table Storage: skalbara och utförda tabeller i Azure Cosmos DB och Azure Table Storage'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: wmengmsft
ms.author: wmeng
ms.custom: seodec18
ms.openlocfilehash: 74bd22de81e385a4fbd9129a70616e24b594b0b4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441327"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Tabell design guide för Azure Table Storage: skalbara och genomförda tabeller

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

För att utforma skalbara och presterande tabeller måste du tänka på en rad olika faktorer, inklusive kostnad. Om du tidigare har utformat scheman för Relations databaser är det viktigt att du är bekant med dessa överväganden. Men det finns vissa likheter mellan Azure Table Storage och Relations modeller, men det finns också många viktiga skillnader. Dessa skillnader leder vanligt vis till olika design som kan se till att det är intuitivt eller fel i någon bekant relation mellan Relations databaser, men det är viktigt om du designar för ett NoSQL nyckel/värde-lager, till exempel Table Storage.

Table Storage är utformat för att stödja molnbaserade program som kan innehålla miljard tals entiteter ("rader" i Relations databas terminologi) eller för data uppsättningar som måste ha stöd för hög transaktions volymer. Du måste därför tänka på olika sätt att lagra dina data och förstå hur Table Storage fungerar. Ett väl utformat NoSQL-datalager kan göra det möjligt för din lösning att skala mycket ytterligare (och en lägre kostnad) än en lösning som använder en Relations databas. Den här guiden hjälper dig med de här avsnitten.  

## <a name="about-azure-table-storage"></a>Om Azure Table Storage
I det här avsnittet beskrivs några av de viktigaste funktionerna i Table Storage som är särskilt relevanta för att utforma för prestanda och skalbarhet. Om du inte har använt Azure Storage-och table Storage tidigare kan du läsa [Introduktion till Microsoft Azure Storage](../storage/common/storage-introduction.md) och [komma igång med Azure Table Storage med hjälp av .net](table-storage-how-to-use-dotnet.md) innan du läser resten av den här artikeln. Även om den här hand boken är i tabell lagring, innehåller den viss diskussion om Azure Queue Storage och Azure Blob Storage, samt hur du kan använda dem tillsammans med Table Storage i en lösning.  

Tabell lagring använder ett tabell format för att lagra data. Varje rad i tabellen representerar en entitet i standard-terminologi och kolumnerna lagra de olika egenskaperna för denna entitet. Varje entitet har ett nyckel par för att unikt identifiera den och en tidsstämpelkolumn som tabell lagring använder för att spåra när entiteten senast uppdaterades. Tidsstämpel-fältet läggs till automatiskt och du kan inte skriva över tidsstämpeln manuellt med ett godtyckligt värde. I Table Storage används den senaste ändrade tidsstämpeln (LMT) för att hantera optimistisk samtidighet.  

> [!NOTE]
> REST API åtgärder för tabell lagring returnerar också ett `ETag` värde som den härleds från LMT. I det här dokumentet används ETag-och LMT-termerna utbytbara, eftersom de refererar till samma underliggande data.  
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
<td>Marketing</td>
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
<td>Marketing</td>
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
<td>Marketing</td>
<td>Avdelning</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
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


Hittills ser den här designen ut ungefär som en tabell i en Relations databas. De viktigaste skillnaderna är de obligatoriska kolumnerna och möjligheten att lagra flera entitetstyper i samma tabell. Dessutom har var och en av de användardefinierade egenskaperna, till exempel **FirstName** eller **Age**, en datatyp, till exempel Integer eller string, precis som en kolumn i en Relations databas. Till skillnad från i en Relations databas innebär dock schema fri form av tabell lagring att en egenskap inte behöver ha samma datatyp på varje entitet. För att lagra komplexa datatyper i en enskild egenskap, måste du använda ett serialiserade format som JSON eller XML. Mer information finns i [förstå tabell lagrings data modell](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Ditt val av `PartitionKey` och `RowKey` är grundläggande för en bra tabell design. Varje entitet som lagras i en tabell måste ha en unik kombination av `PartitionKey` och `RowKey`. Precis som med nycklar i en Relations databas tabell indexeras `PartitionKey`-och `RowKey`s värden för att skapa ett grupperat index som aktiverar snabb uppslags fönster. Table Storage skapar dock inte några sekundära index, så dessa är de enda två indexerade egenskaperna (några av mönstren som beskrivs senare visar hur du kan kringgå den här synliga begränsningen).  

En tabell består av en eller flera partitioner, och många av de design beslut du gör är att välja lämplig `PartitionKey` och `RowKey` för att optimera din lösning. En lösning kan bestå av bara en enda tabell som innehåller alla entiteter som är ordnade i partitioner, men vanligt vis har en lösning flera tabeller. Tabeller hjälper dig att logiskt organisera dina entiteter och hjälper dig att hantera åtkomst till data med hjälp av åtkomst kontrol listor. Du kan släppa en hel tabell genom att använda en enda lagrings åtgärd.  

### <a name="table-partitions"></a>Tabellpartitioner
Konto namnet, tabell namnet och `PartitionKey` tillsammans identifierar partitionen i lagrings tjänsten där Table Storage lagrar entiteten. Som en del av adresserings planen för entiteter definierar partitionerna en omfattning för transaktioner (se avsnittet senare i den här artikeln, i [enhets grupp transaktioner](#entity-group-transactions)) och utgör grunden för hur tabell lagring skalar. Mer information om tabellattribut finns i [Check lista för prestanda och skalbarhet för Table Storage](../storage/tables/storage-performance-checklist.md).  

I Table Storage är en enskild nod en eller flera fullständiga partitioner, och tjänsten skalas genom en dynamisk belastnings utjämning mellan noderna. Om en nod är under belastning kan Table Storage dela upp det intervall av partitioner som hanteras av noden på olika noder. Vid trafik under sidor kan tabell lagring sammanfoga partitionernas intervall från tysta noder tillbaka till en enda nod.  

Mer information om intern information om Table Storage och hur den hanterar partitioner finns i [Microsoft Azure Storage: en moln lagrings tjänst med hög tillgänglighet med stark konsekvens](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Enhets grupp transaktioner
I Table Storage är enhets grupp transaktioner (EGTs) den enda inbyggda mekanismen för att utföra atomiska uppdateringar över flera entiteter. EGTs kallas även *batch-transaktioner*. EGTs kan bara användas på entiteter som är lagrade i samma partition (som delar samma partitionsnyckel i en viss tabell), så när som helst behöver du Atomic-transaktionell beteende över flera entiteter, se till att dessa entiteter finns i samma partition. Detta är ofta en orsak till att hålla flera entitetstyper i samma tabell (och partition) och inte använda flera tabeller för olika typer av enheter. En enda EGT tillämpas på högst 100 entiteter.  Om du skickar flera samtidiga EGTs för bearbetning är det viktigt att se till att dessa EGTs inte körs på entiteter som är gemensamma för EGTs. Annars riskerar du att fördröja bearbetningen.

EGTs introducerar också en potentiell kompromiss som du kan använda för att utvärdera i din design. Genom att använda fler partitioner ökar du skalbarheten för ditt program eftersom Azure har fler möjligheter till belastnings Utjämnings begär Anden mellan noder. Men detta kan begränsa möjligheten för ditt program att utföra atomiska transaktioner och upprätthålla stark konsekvens för dina data. Dessutom finns det vissa skalbara mål på nivån för en partition som kan begränsa data flödet för transaktioner som du kan förväntar dig för en enda nod.

Mer information om skalbarhets mål för Azure Storage-konton finns i [skalbarhets mål för standard lagrings konton](../storage/common/scalability-targets-standard-account.md). Mer information om skalbarhets mål för Table Storage finns i [skalbarhets-och prestanda mål för Table Storage](../storage/tables/scalability-targets.md). Senare avsnitt i den här guiden beskrivs olika design strategier som hjälper dig att hantera avvägningarna som den här och diskutera hur du bäst för att välja din partitionsnyckel baserat på de specifika kraven för klientprogrammet.  

### <a name="capacity-considerations"></a>Överväganden för kapacitet
Följande tabell innehåller några av de viktigaste värdena som du bör känna till när du skapar en lösning för tabell lagring:  

| Total kapacitet för ett Azure storage-konto | 500 TB |
| --- | --- |
| Antalet tabeller i ett Azure storage-konto |Begränsas bara av lagrings kontots kapacitet. |
| Antalet partitioner i en tabell |Begränsas bara av lagrings kontots kapacitet. |
| Antal entiteter i en partition |Begränsas bara av lagrings kontots kapacitet. |
| Storleken på en enskild entitet |Upp till 1 MB, med högst 255 egenskaper (inklusive `PartitionKey`, `RowKey`och `Timestamp`). |
| Storlek på `PartitionKey` |En sträng som är upp till 1 KB stor. |
| Storlek på `RowKey` |En sträng som är upp till 1 KB stor. |
| Storlek på en enhets grupps transaktion |En transaktion kan innehålla högst 100 entiteter och nytto lasten måste vara mindre än 4 MB. En entitet kan bara uppdatera en gång i en EGT. |

Mer information finns i [förstå Table service data modell](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Kostnadsöverväganden
Table Storage är relativt billigt, men du bör inkludera kostnads uppskattningar för både kapacitets användning och antalet transaktioner som en del av din utvärdering av en lösning som använder Table Storage. I många fall är det dock ett giltigt tillvägagångs sätt att lagra denormaliserade eller duplicerade data för att förbättra din lösnings prestanda eller skalbarhet. Mer information om priser finns i [Azure Storage-priser](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Riktlinjer för tabelldesign
Listorna sammanfattar några av de viktigaste rikt linjerna som du bör tänka på när du skapar tabeller. I den här guiden hittar du mer information senare. Dessa rikt linjer skiljer sig från rikt linjerna som du vanligt vis följer för Relations databas design.  

Designa din tabell lagring så att den blir *mer* effektiv:

* **Design för frågor i Läs-aktiverat program.** När du skapar tabeller bör du tänka på frågorna (särskilt svars känsliga) som du kör innan du funderar på hur du ska uppdatera dina entiteter. Detta innebär vanligtvis en effektiv och högpresterande lösning.  
* **Ange både `PartitionKey` och `RowKey` i dina frågor.** *Punkt frågor* som dessa är de mest effektiva tabell lagrings frågorna.  
* **Överväg att lagra dubbletter av entiteter.** Table Storage är billigt, så Överväg att lagra samma entitet flera gånger (med olika nycklar) för att aktivera mer effektiva frågor.  
* **Överväg att avnormalisera data.** Table Storage är billigt, så Överväg att avnormalisera dina data. Till exempel lagra sammanfattning entiteter så att frågor för aggregerade data bara behöver åtkomst till en enda entitet.  
* **Använd sammansatt nyckelvärden.** De enda nycklar du har är `PartitionKey` och `RowKey`. Till exempel använda sammansatt nyckelvärden för att aktivera alternativa knappade åtkomstsökvägar entiteter.  
* **Använd fråga projektion.** Du kan minska mängden data som du överför över nätverket med hjälp av frågor som väljer enbart de fält som du behöver.  

Designa din tabell lagring så att den blir *Skriv* effektiv:  

* **Skapa inte varma partitioner.** Välj nycklar som gör det möjligt att sprida dina förfrågningar över flera partitioner vid någon tidpunkt.  
* **Undvik trafiktoppar.** Distribuera trafiken under en rimlig tids period och Undvik toppar i trafiken.
* **Inte nödvändigtvis att skapa en separat tabell för varje typ av entiteten.** När du behöver atomiska transaktioner över entitetstyper kan lagra du dessa flera typer av enheter i samma partition i samma tabell.
* **Överväg att det maximala dataflöde som du måste uppnå.** Du måste vara medveten om skalbarhets målen för Table Storage och se till att designen inte gör att du överskrider den.  

Senare i den här hand boken får du exempel på hur du kan lägga till alla dessa principer i praktiken.  

## <a name="design-for-querying"></a>Utforma för frågor
Table Storage kan vara en läsnings intensiv, skriv intensiv eller en blandning av de två. I det här avsnittet förklaras utformningen av att stödja Läs åtgärder effektivt. Normalt är också en design som stöder läsåtgärder effektivt effektivt för skrivåtgärder. Det finns dock ytterligare överväganden när du utformar för att stödja Skriv åtgärder. Dessa beskrivs i nästa avsnitt, [design för data ändringar](#design-for-data-modification).

En bra utgångs punkt så att du kan läsa data effektivt är att fråga "vilka frågor måste jag köra för att hämta de data som krävs?"  

> [!NOTE]
> Med Table Storage är det viktigt att du får fram rätt design, eftersom det är svårt och dyrt att ändra det senare. I en Relations databas är det ofta möjligt att åtgärda prestanda problem genom att lägga till index i en befintlig databas. Detta är inte ett alternativ med Table Storage.  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>Hur du väljer `PartitionKey` och `RowKey` påverkar frågans prestanda
I följande exempel förutsätter vi att tabell lagring lagrar anställdas entiteter med följande struktur (de flesta av exemplen tar inte med `Timestamp`-egenskapen för tydlighetens skull):  

| Kolumnnamn | Datatyp |
| --- | --- |
| `PartitionKey` (avdelnings namn) |String |
| `RowKey` (medarbetar-ID) |String |
| `FirstName` |String |
| `LastName` |String |
| `Age` |Integer |
| `EmailAddress` |String |

Här följer några allmänna rikt linjer för att utforma tabell lagrings frågor. Filter-syntaxen som används i följande exempel är från tabell lagrings REST API. Mer information finns i [fråga entiteter](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* En *punkt fråga* är den mest effektiva sökningen som används och rekommenderas för sökning efter stora volymer eller uppslag som kräver lägsta latens. En sådan fråga kan använda indexen för att hitta en enskild entitet effektivt genom att ange både `PartitionKey`-och `RowKey` värden. Till exempel: `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`.  
* Det andra bästa är en *Range-fråga*. Den använder `PartitionKey`och filter på ett intervall med `RowKey` värden för att returnera mer än en entitet. Värdet `PartitionKey` identifierar en viss partition och `RowKey` värden identifierar en delmängd av entiteterna i partitionen. Till exempel: `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`.  
* Det tredje bästa är en *partitions ökning*. Den använder `PartitionKey`och filter på en annan icke-nyckel-egenskap och kan returnera fler än en entitet. Värdet `PartitionKey` identifierar en viss partition och egenskaps värden väljer för en delmängd av entiteterna i den partitionen. Till exempel: `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`.  
* En *tabells ökning* omfattar inte `PartitionKey`och är ineffektiv eftersom den söker igenom alla partitioner som utgör tabellen för matchande entiteter. Den utför en tabells ökning oavsett om ditt filter använder `RowKey`. Till exempel: `$filter=LastName eq 'Jones'`.  
* Azure Table Storage-frågor som returnerar flera entiteter sorteras i `PartitionKey` och `RowKey`s ordning. Välj en `RowKey` som definierar den vanligaste sorterings ordningen för att undvika att enheterna i klienten används. Frågeresultat som returneras av Azure-Tabell-API i Azure Cosmos DB sorteras inte efter partitionsnyckel eller rad nyckel. En detaljerad lista över funktions skillnader finns i [skillnader mellan tabell-API i Azure Cosmos DB och Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Om du använder en "**eller**" för att ange ett filter baserat på `RowKey` värden resulterar det i en partitions ökning och behandlas inte som en områdes fråga. Undvik därför frågor som använder filter som till exempel: `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`.  

Exempel på kod på klient sidan som använder lagrings klient biblioteket för att köra effektiva frågor finns i:  

* [Köra en punkt fråga med hjälp av lagrings klient biblioteket](#run-a-point-query-by-using-the-storage-client-library)
* [Hämta flera entiteter med hjälp av LINQ](#retrieve-multiple-entities-by-using-linq)
* [Projektion för serversidan](#server-side-projection)  

Exempel på klientsidan kod som kan hantera flera enhetstyper lagras i samma tabell finns:  

* [Arbeta med heterogena entitetstyper](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>Välj lämplig `PartitionKey`
Ditt val av `PartitionKey` bör balansera behovet av att möjliggöra användningen av EGTs (för att säkerställa konsekvens) mot kravet att distribuera dina entiteter över flera partitioner (för att säkerställa en skalbar lösning).  

I ett extrema läge kan du lagra alla entiteter i en enda partition. Men detta kan begränsa din lösnings skalbarhet och förhindra att tabell lagring kan läsa in-balans-begäranden. Du kan lagra en entitet per partition på de andra extrema. Detta är mycket skalbart och möjliggör tabell lagring för belastnings Utjämnings begär Anden, men förhindrar att du använder enhets grupp transaktioner.  

Med en idealisk `PartitionKey` kan du använda effektiva frågor och har tillräckligt med partitioner för att säkerställa att din lösning är skalbar. Normalt ser du att dina enheter har en lämplig egenskap som distribuerar dina entiteter över tillräckligt många partitioner.

> [!NOTE]
> I ett system som lagrar information om användare eller anställda kan `UserID` till exempel vara en lämplig `PartitionKey`. Du kan ha flera entiteter som använder en viss `UserID` som partitionsnyckel. Varje entitet som lagrar data om en användare grupperas i en enda partition. Dessa entiteter är tillgängliga via EGTs, samtidigt som de är mycket skalbara.
> 
> 

Det finns ytterligare överväganden i ditt val av `PartitionKey` som relaterar till hur du infogar, uppdaterar och tar bort entiteter. Mer information finns i [design för ändring av data](#design-for-data-modification) senare i den här artikeln.  

### <a name="optimize-queries-for-table-storage"></a>Optimera frågor för tabell lagring
Table Storage indexerar automatiskt dina entiteter med hjälp av `PartitionKey` och `RowKey` värden i ett enda grupperat index. Detta är anledningen till att punkt frågor är mest effektiva att använda. Det finns dock inga andra index än det i det grupperade indexet på `PartitionKey` och `RowKey`.

Många design måste uppfylla kraven för att aktivera sökning efter enheter baserat på flera villkor. Du kan till exempel hitta anställdas entiteter baserat på e-post, medarbetar-ID eller efter namn. Följande mönster i [design mönster](#table-design-patterns) för avsnitts tabeller hanterar de här typerna av krav. Mönstren beskriver också olika sätt att arbeta runt det faktum att Table Storage inte tillhandahåller sekundära index.  

* [Sekundärt index mönster för sekundär partition](#intra-partition-secondary-index-pattern): lagra flera kopior av varje entitet genom att använda olika `RowKey` värden (i samma partition). Detta möjliggör snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika `RowKey` värden.  
* [Sekundärt index mönster mellan partitioner](#inter-partition-secondary-index-pattern): lagra flera kopior av varje entitet genom att använda olika `RowKey` värden i separata partitioner eller i separata tabeller. Detta möjliggör snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika `RowKey` värden.  
* [Mönster för index entiteter](#index-entities-pattern): underhåll index enheter för att aktivera effektiva sökningar som returnerar listor med entiteter.  

### <a name="sort-data-in-table-storage"></a>Sortera data i Table Storage

Table Storage returnerar frågeresultat sorterade i stigande ordning, baserat på `PartitionKey` och sedan efter `RowKey`.

> [!NOTE]
> Frågeresultat som returneras av Azure-Tabell-API i Azure Cosmos DB sorteras inte efter partitionsnyckel eller rad nyckel. En detaljerad lista över funktions skillnader finns i [skillnader mellan tabell-API i Azure Cosmos DB och Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Nycklar i Table Storage är sträng värden. För att se till att numeriska värden sorteras korrekt bör du omvandla dem till en fast längd och fylla dem med nollor. Om värdet för medarbetar-ID: t som du använder som `RowKey` är ett heltals värde, bör du konvertera anställnings-ID **123** till **00000123**. 

Många program har kraven för att använda data som sorterats i olika ordning: till exempel sortera anställda efter namn eller genom att gå med datum. Följande mönster i avsnittets [design mönster](#table-design-patterns) för avsnitts design gör hur du kan använda alternativa sorterings ordningar för entiteterna:  

* [Sekundärt index mönster för sekundär partition](#intra-partition-secondary-index-pattern): lagra flera kopior av varje entitet genom att använda olika `RowKey` värden (i samma partition). Detta möjliggör snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika `RowKey` värden.  
* [Sekundärt index mönster mellan partitioner](#inter-partition-secondary-index-pattern): lagra flera kopior av varje entitet genom att använda olika `RowKey` värden i separata partitioner i separata tabeller. Detta möjliggör snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika `RowKey` värden.
* [Logg änden-mönster](#log-tail-pattern): Hämta de *n* entiteter som senast har lagts till i en partition genom att använda ett `RowKey` värde som sorterar efter omvänt datum-och tids ordning.  

## <a name="design-for-data-modification"></a>Utforma för dataändring
Det här avsnittet fokuserar på designöverväganden för att optimera infogningar, uppdateringar och borttagningar. I vissa fall måste du utvärdera de olika design modeller som optimeras för frågor mot designer som optimerar för data ändringar. Den här utvärderingen liknar vad du gör i design för Relations databaser (även om teknikerna för att hantera de här design handels utfallen är olika i en Relations databas). [Design mönster](#table-design-patterns) för avsnitts tabell beskriver några detaljerade design mönster för tabell lagring och visar några av dessa kompromisser. I praktiken kommer du att se att många design som är optimerade för att fråga entiteter också fungerar bra för att ändra entiteter.  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optimera prestanda för INSERT-, Update-och Delete-åtgärder
Om du vill uppdatera eller ta bort en entitet måste du kunna identifiera den med hjälp av `PartitionKey` och `RowKey` värden. I detta hänseende bör ditt val av `PartitionKey` och `RowKey` för att ändra entiteter följa liknande kriterier som du väljer för att kunna använda punkt frågor. Du vill identifiera entiteter så effektivt som möjligt. Du vill inte använda en ineffektiv partition eller tabells ökning för att hitta en entitet för att identifiera `PartitionKey` och `RowKey` värden som du behöver uppdatera eller ta bort.  

Följande mönster i avsnittets [design mönster](#table-design-patterns) adress optimerar prestandan för åtgärderna för att infoga, uppdatera och ta bort:  

* [Mönster för hög volym borttagning](#high-volume-delete-pattern): Aktivera borttagning av en stor mängd entiteter genom att lagra alla entiteter för samtidig borttagning i en egen separat tabell. Du tar bort entiteterna genom att ta bort tabellen.  
* [Mönster för data serier](#data-series-pattern): lagra kompletta data serier i en enskild entitet för att minimera antalet begär Anden som du gör.  
* [Mönster för breda entiteter](#wide-entities-pattern): Använd flera fysiska entiteter för att lagra logiska entiteter med fler än 252 egenskaper.  
* [Mönster för stora entiteter](#large-entities-pattern): Använd Blob Storage för att lagra stora egenskaps värden.  

### <a name="ensure-consistency-in-your-stored-entities"></a>Säkerställa konsekvens i dina lagrade entiteter
Den viktiga faktor som påverkar ditt val av nycklar för att optimera dataändringar är att säkerställa konsekvens med hjälp av atomiska transaktioner. Du kan bara använda en EGT för att arbeta med entiteter som lagras i samma partition.  

Följande mönster i avsnittets [design mönster](#table-design-patterns) adress hanterar konsekvens:  

* [Sekundärt index mönster för sekundär partition](#intra-partition-secondary-index-pattern): lagra flera kopior av varje entitet genom att använda olika `RowKey` värden (i samma partition). Detta möjliggör snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika `RowKey` värden.  
* [Sekundärt index mönster mellan partitioner](#inter-partition-secondary-index-pattern): lagra flera kopior av varje entitet genom att använda olika `RowKey` värden i separata partitioner eller i separata tabeller. Detta möjliggör snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika `RowKey` värden.  
* [Mönster för till sist konsekventa transaktioner](#eventually-consistent-transactions-pattern): Aktivera konsekvent beteende för partitionerings gränser eller lagrings system gränser med hjälp av Azure-köer.
* [Mönster för index entiteter](#index-entities-pattern): underhåll index enheter för att aktivera effektiva sökningar som returnerar listor med entiteter.  
* [Utnormaliserings mönster](#denormalization-pattern): kombinera relaterade data i en enda entitet så att du kan hämta alla data du behöver med en enda punkt fråga.  
* [Mönster för data serier](#data-series-pattern): lagra kompletta data serier i en enda entitet, för att minimera antalet förfrågningar som du gör.  

Mer information finns i [enhets grupp transaktioner](#entity-group-transactions) längre fram i den här artikeln.  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Se till att designen för effektiva ändringar underlättar effektiva frågor
En design för effektiva frågor ger effektiv ändringar, men du bör alltid i många fall kan utvärdera om så är fallet för ditt specifika scenario. Några av mönstren i [design mönster](#table-design-patterns) för avsnitts tabeller utvärderar explicit kompromisser mellan frågor och ändring av entiteter, och du bör alltid ta hänsyn till antalet av varje typ av åtgärd.  

Följande mönster i [design mönster](#table-design-patterns) för avsnitts tabeller riktar in dig mellan att utforma för effektiva frågor och design för effektiv data ändring:  

* [Sammansatt nyckel mönster](#compound-key-pattern): Använd sammansatta `RowKey` värden för att göra det möjligt för en klient att söka efter relaterade data med en enda punkt fråga.  
* [Logg änden-mönster](#log-tail-pattern): Hämta de *n* entiteter som senast har lagts till i en partition genom att använda ett `RowKey` värde som sorterar efter omvänt datum-och tids ordning.  

## <a name="encrypt-table-data"></a>Kryptera tabell data
Klient biblioteket för .NET-Azure Storage stöder kryptering av sträng egenskaper för enhets egenskaper för INSERT-och replace-åtgärder. De krypterade strängarna lagras i tjänsten som binära egenskaper, och de konverteras tillbaka till strängar efter dekrypteringen.    

För tabeller, utöver krypteringsprincipen, måste användare ange egenskaper som ska krypteras. Ange antingen ett `EncryptProperty`-attribut (för POCO-entiteter som härleds från `TableEntity`) eller ange en krypterings lösare i alternativ för begäran. En krypterings lösare är ett ombud som tar en partitionsnyckel, rad nyckel och egenskaps namn och returnerar ett booleskt värde som anger om den egenskapen ska krypteras. Under krypteringen använder klient biblioteket den här informationen för att avgöra om en egenskap ska krypteras när den skrivs till kabeln. Delegaten ger också möjlighet att logic kring hur egenskaper som är krypterade. (Till exempel om X, krypterar sedan egenskap A, annars krypterar egenskaper A och B.) Du behöver inte ange den här informationen samtidigt som du läser eller frågar entiteter.

Sammanslagning stöds inte för närvarande. Eftersom en delmängd av egenskaperna kan ha krypterats tidigare med hjälp av en annan nyckel, så resulterar det i data förlust om du helt enkelt slår samman de nya egenskaperna och uppdaterar metadata. Sammanslagning av kräver att du gör extra tjänst anrop för att läsa den befintliga entiteten från tjänsten, eller genom att använda en ny nyckel per egenskap. Ingen av dessa är lämpliga av prestanda skäl.     

Information om hur du krypterar tabell data finns i [kryptering på klient sidan och Azure Key Vault för Microsoft Azure Storage](../storage/common/storage-client-side-encryption.md).  

## <a name="model-relationships"></a>Modell relationer
Modellutveckling domän är ett viktigt steg i utformningen av komplexa system. Normalt använder du modellerings processen för att identifiera entiteter och relationerna mellan dem, som ett sätt att förstå företags domänen och informera utformningen av ditt system. Det här avsnittet fokuserar på hur du kan översätta några av de vanliga Relations typerna i domän modeller till design för Table Storage. Processen för att mappa från en logisk data modell till en fysisk NoSQL data modell skiljer sig från den som används när du skapar en Relations databas. Design av Relations databaser förutsätter vanligt vis en data normaliserings process som är optimerad för att minimera redundans. Sådan design förutsätter också en deklarativ fråge funktion som sammanfattar implementeringen av hur databasen fungerar.  

### <a name="one-to-many-relationships"></a>En-till-många-relationer
En-till-många-relationer mellan företag domänobjekt inträffar ofta: till exempel en avdelning har många anställda. Det finns flera sätt att implementera en-till-många-relationer i Table Storage, var och en med-och-och nack delar som kan vara relevanta för det specifika scenariot.  

Överväg exemplet på ett stort multinationella företag med flera tusentals avdelningar och personal enheter. Varje avdelning har många anställda och varje medarbetare är kopplad till en speciell avdelning. En metod är att lagra separata avdelnings-och personal enheter, till exempel följande:  

![Bild som visar en avdelnings enhet och en anställds entitet][1]

Det här exemplet visar en implicit en-till-många-relation mellan typerna, baserat på `PartitionKey` värde. Varje avdelning kan ha många anställda.  

Det här exemplet visar också en avdelning entitet och entiteter relaterade anställda i samma partition. Du kan välja att använda olika partitioner, tabeller eller till och med lagrings konton för de olika enhets typerna.  

En annan metod är att avnormalisera dina data och endast lagra anställdas enheter med avnormaliserade avdelnings data, som du ser i följande exempel. I det här scenariot kanske det här avnormaliserade tillvägagångs sättet inte är det bästa om du har ett krav för att kunna ändra information om en avdelnings chef. För att göra detta måste du uppdatera varje anställd på avdelningen.  

![Bild av entiteten personal][2]

Mer information finns i den [Denormalisering mönstret](#denormalization-pattern) senare i den här guiden.  

I följande tabell sammanfattas både för-och nack delar med var och en av metoderna för att lagra personal-och avdelnings enheter som har en en-till-många-relation. Du bör också fundera över hur ofta du förväntar dig att utföra olika åtgärder. Det kan vara acceptabelt att ha en design som innehåller en dyr åtgärd om den här åtgärden bara sker sällan.  

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
<li>Du kan behöva hämta både en anställd och en avdelnings enhet för vissa klient aktiviteter.</li>
<li>Lagringsåtgärder inträffa i samma partition. På hög transaktions volymer kan detta resultera i en aktiv punkt.</li>
<li>Du kan inte flytta en medarbetare till en ny avdelning med hjälp av en EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Separata entitetstyper, olika partitioner eller tabeller eller storage-konton</td>
<td>
<ul>
<li>Du kan uppdatera en avdelning entitet eller medarbetare enhet med en enda åtgärd.</li>
<li>På hög transaktions volymer kan detta hjälpa till att sprida belastningen över flera partitioner.</li>
</ul>
</td>
<td>
<ul>
<li>Du kan behöva hämta både en anställd och en avdelnings enhet för vissa klient aktiviteter.</li>
<li>Du kan inte använda EGTs för att upprätthålla konsekvens när du uppdaterar/infogar/tar bort en medarbetare och uppdaterar en avdelning. Till exempel uppdatering av ett antal anställda i en avdelning entitet.</li>
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
<li>Det kan vara dyrt att upprätthålla konsekvens om du behöver uppdatera avdelnings information (Detta kräver att du uppdaterar alla anställda på en avdelning).</li>
</ul>
</td>
</tr>
</table>

Hur du väljer bland dessa alternativ och vilka av-och nack delar som är mest signifikanta beror på dina specifika program scenarier. Till exempel hur ofta ändrar du avdelnings enheter? Behöver alla dina anställda frågor ytterligare avdelnings information? Hur nära är du på skalbarhets gränserna för dina partitioner eller ditt lagrings konto?  

### <a name="one-to-one-relationships"></a>Motsvarighet
Domän modeller kan innehålla en-till-en-relationer mellan entiteter. Om du behöver implementera en en-till-en-relation i Table Storage måste du också välja hur du länkar de två relaterade entiteterna när du behöver hämta dem. Den här länken kan antingen vara implicit, baserat på en konvention i nyckel värden eller explicit, genom att lagra en länk i form av `PartitionKey` och `RowKey` värden i varje entitet till den relaterade entiteten. En diskussion om huruvida du ska lagra relaterade entiteter i samma partition, finns i avsnittet [en-till-många-relationer](#one-to-many-relationships).  

Det finns också implementerings överväganden som kan leda till att du implementerar en-till-en-relationer i Table Storage:  

* Hantering av stora entiteter (mer information finns i [mönster för stora entiteter](#large-entities-pattern)).  
* Implementera åtkomst kontroller (mer information finns i [kontrol lera åtkomst med signaturer för delad åtkomst](#control-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Gå med i klienten
Även om det finns olika sätt att modellera relationer i Table Storage, kan du inte glömma att de två vanligaste orsakerna till att använda Table Storage är skalbarhet och prestanda. Om du hittar flera relationer som äventyrar prestanda och skalbarhet för din lösning bör du fråga dig själv om det är nödvändigt att bygga alla data relationer i tabell designen. Du kanske kan förenkla designen och förbättra skalbarheten och prestandan för din lösning, om du låter klient programmet utföra alla nödvändiga kopplingar.  

Om du till exempel har små tabeller som innehåller data som inte ändras ofta kan du hämta dessa data en gång och cachelagra dem på klienten. Detta kan undvika upprepade görs för att hämta samma data. I de exempel som vi har tittat på i den här hand boken är en uppsättning avdelningar i en liten organisation troligt vis liten och ändras sällan. Detta gör det en bra kandidat för data som ett klient program kan ladda ned en gång och cachelagra som sökdata.  

### <a name="inheritance-relationships"></a>Arvsrelationer
Om klient programmet använder en uppsättning klasser som utgör en del av en arvs relation som representerar affär senheter, kan du enkelt spara dessa entiteter i Table Storage. Du kan till exempel ha följande uppsättning klasser definierade i klient programmet, där `Person` är en abstrakt klass.

![Diagram över arvs relationer][3]

Du kan bevara instanser av de två konkreta klasserna i Table Storage med hjälp av en enda `Person` tabell. Använd entiteter som ser ut ungefär så här:  

![Bild som visar entiteten kund och anställd][4]

Mer information om hur du arbetar med flera olika entitetstyper i samma tabell i klient koden finns i [arbeta med heterogena entitetstyper](#work-with-heterogeneous-entity-types) senare i den här hand boken. Detta ger exempel på hur du känner igen entitetstypen i klientkod.  

## <a name="table-design-patterns"></a>Mönster för tabelldesign
I föregående avsnitt har du lärt dig hur du optimerar tabell designen för att hämta enhets data med hjälp av frågor och för att infoga, uppdatera och ta bort entitetsposter. I det här avsnittet beskrivs några mönster som lämpar sig för användning med Table Storage. Dessutom får du se hur du praktiskt taget kan åtgärda några av de problem och kompromisser som uppstått tidigare i den här hand boken. Följande diagram sammanfattar relationerna mellan olika mönster:  

![Diagram över tabell design mönster][5]

Mönster kartan visar några relationer mellan mönster (blå) och anti-mönster (orange) som dokumenteras i den här guiden. Det är naturligtvis många andra mönster som är värda att hänsyn tagits till. Ett av de viktigaste scenarierna för Table Storage är till exempel att använda [mönstret för materialiserade vyer](https://msdn.microsoft.com/library/azure/dn589782.aspx) från [kommando frågans ansvars](https://msdn.microsoft.com/library/azure/jj554200.aspx) mönster.  

### <a name="intra-partition-secondary-index-pattern"></a>Mönster för Intra-partition sekundärt index
Lagra flera kopior av varje entitet genom att använda olika `RowKey` värden (i samma partition). Detta möjliggör snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika `RowKey` värden. Uppdateringar mellan kopior kan behållas konsekvent med hjälp av EGTs.  

#### <a name="context-and-problem"></a>Kontext och problem
Table Storage indexerar automatiskt entiteter med hjälp av `PartitionKey` och `RowKey` värden. Detta gör att ett klient program kan hämta en entitet effektivt genom att använda dessa värden. Om du till exempel använder följande tabell struktur kan ett klient program använda en punkt fråga för att hämta en enskild anställd entitet med hjälp av avdelnings namnet och anställnings-ID: t (`PartitionKey` och `RowKey` värden). En-klient kan också hämta entiteter sorterade efter anställnings-ID inom varje avdelning.

![Bild av entiteten personal][6]

Om du även vill hitta en anställd entitet baserat på värdet för en annan egenskap, till exempel e-postadress, måste du använda en mindre effektiv partitions ökning för att hitta en matchning. Detta beror på att Table Storage inte tillhandahåller sekundära index. Det finns dessutom inget alternativ för att begära en lista över anställda sorterad i en annan ordning än `RowKey` ordning.  

#### <a name="solution"></a>Lösning
För att undvika avsaknad av sekundära index kan du lagra flera kopior av varje entitet, med varje kopia med ett annat `RowKey` värde. Om du lagrar en entitet med följande strukturer kan du effektivt hämta personal enheter baserat på e-postadress eller medarbetar-ID. Prefixvärde för `RowKey`, `empid_`och `email_` gör att du kan fråga efter en enskild anställd eller ett antal anställda genom att använda ett intervall med e-postadresser eller anställnings-ID.  

![Bild som visar anställdas entitet med varierande RowKey-värden][7]

Följande två filter villkor (en sökning efter anställnings-ID och en sökning efter e-postadress) anger båda punkt frågorna:  

* $filter = (PartitionKey eq ”försäljning”) och (RowKey eq 'empid_000223 ”)  
* $filter = (PartitionKey eq ”försäljning”) och (RowKey eq 'email_jonesj@contoso.com”)  

Om du frågar efter ett intervall med anställdas entiteter kan du ange ett intervall som sorteras i anställnings-ID-ordning, eller ett intervall som sorteras i e-postadressen. Fråga efter entiteter med lämpligt prefix i `RowKey`.  

* Om du vill hitta alla anställda på försäljnings avdelningen med ett anställnings-ID i intervallet 000100 till 000199 använder du: $filter = (PartitionKey EQ Sales) och (RowKey ge "empid_000100") och (RowKey Le ' empid_000199 ')  
* Om du vill hitta alla anställda på försäljnings avdelningen med en e-postadress som börjar med bokstaven "a" använder du: $filter = (PartitionKey EQ Sales) och (RowKey ge "email_a") och (RowKey lt ' email_b ')  
  
Den filter-syntax som används i föregående exempel är från tabell lagrings REST API. Mer information finns i [fråga entiteter](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Table Storage är relativt billigt att använda, så kostnads belastningen för lagring av dubblettdata bör inte vara en stor angelägenhet. Du bör dock alltid utvärdera kostnaden för din design baserat på dina förväntade lagrings krav och bara lägga till dubbla entiteter som stöder de frågor som klient programmet ska köra.  
* Eftersom de sekundära index enheterna lagras på samma partition som de ursprungliga entiteterna, bör du se till att du inte överskrider skalbarhets målen för en enskild partition.  
* Du kan behålla dina dubbla entiteter överensstämmer med varandra med hjälp av EGTs för att uppdatera två kopior av entiteten atomiskt. Detta innebär att du ska lagra alla kopior av en entitet i samma partition. Mer information finns i [använda enhets grupp transaktioner](#entity-group-transactions).  
* Värdet som används för `RowKey` måste vara unikt för varje entitet. Överväg att använda sammansatt nyckelvärden.  
* Utfyllnad av numeriska värden i `RowKey` (till exempel anställnings-ID 000223) möjliggör korrekt sortering och filtrering baserat på övre och nedre gränser.  
* Du behöver inte duplicera alla egenskaper för entiteten. Till exempel, om frågorna som söker efter entiteter med hjälp av e-postadressen i `RowKey` aldrig behöver den anställdas ålder, kan dessa entiteter ha följande struktur:

  ![Bild av entiteten personal][8]

* Normalt är det bättre att lagra dubblettdata och se till att du kan hämta alla data du behöver med en enda fråga, än att använda en fråga för att hitta en entitet och en annan för att leta upp nödvändiga data.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret i sådana här scenarier:

- Ditt klient program måste hämta entiteter genom att använda en mängd olika nycklar.
- Klienten måste hämta entiteter i olika sorterings ordningar.
- Du kan identifiera varje entitet genom att använda en mängd olika unika värden.

Men se till att du inte överskrider gränserna för skalbarhet när du utför enhets ökningar genom att använda de olika `RowKey` värdena.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Mönster för kommunikation mellan sekundära Partitionsindex](#inter-partition-secondary-index-pattern)
* [Sammansatt nyckel mönster](#compound-key-pattern)
* [Enhets grupp transaktioner](#entity-group-transactions)
* [Arbeta med heterogena entitetstyper](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Mönster för kommunikation mellan sekundära Partitionsindex
Lagra flera kopior av varje entitet genom att använda olika `RowKey` värden i separata partitioner eller i separata tabeller. Detta möjliggör snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika `RowKey` värden.  

#### <a name="context-and-problem"></a>Kontext och problem
Table Storage indexerar automatiskt entiteter med hjälp av `PartitionKey` och `RowKey` värden. Detta gör att ett klient program kan hämta en entitet effektivt genom att använda dessa värden. Om du till exempel använder följande tabell struktur kan ett klient program använda en punkt fråga för att hämta en enskild anställd entitet med hjälp av avdelnings namnet och anställnings-ID: t (`PartitionKey` och `RowKey` värden). En-klient kan också hämta entiteter sorterade efter anställnings-ID inom varje avdelning.  

![Bild av entiteten personal][9]

Om du vill ska kunna hitta en anställd entitet som baseras på värdet för en annan egenskap, till exempel e-postadress, måste du använda en mindre effektivt partition genomsökning för att hitta en matchning. Detta beror på att Table Storage inte tillhandahåller sekundära index. Det finns dessutom inget alternativ för att begära en lista över anställda sorterad i en annan ordning än `RowKey` ordning.  

Du förväntar dig en stor mängd transaktioner mot dessa entiteter och vill minimera risken för att tabell lagrings hastigheten begränsar din klient.  

#### <a name="solution"></a>Lösning
För att undvika avsaknad av sekundära index kan du lagra flera kopior av varje entitet, med varje kopia som använder olika `PartitionKey` och `RowKey` värden. Om du lagrar en entitet med följande strukturer kan du effektivt hämta personal enheter baserat på e-postadress eller medarbetar-ID. Prefixvärde för `PartitionKey`, `empid_`och `email_` gör att du kan identifiera vilket index som du vill använda för en fråga.  

![Bild som visar anställd entitet med primärt index och anställd entitet med sekundärt index][10]

Följande två filter villkor (en sökning efter anställnings-ID och en sökning efter e-postadress) anger båda punkt frågorna:  

* $filter = (PartitionKey eq ' empid_Sales ”) och (RowKey eq '000223”)
* $filter = (PartitionKey eq ' email_Sales ”) och (RowKey eq 'jonesj@contoso.com”)  

Om du frågar efter ett intervall med anställdas entiteter kan du ange ett intervall som sorteras i anställnings-ID-ordning, eller ett intervall som sorteras i e-postadressen. Fråga efter entiteter med lämpligt prefix i `RowKey`.  

* Om du vill hitta alla anställda på försäljnings avdelningen med ett anställnings-ID i intervallet **000100** till **000199**, sorterade i anställnings-ID-ordning, använder du: $filter = (PartitionKey EQ ' empid_Sales ') och (RowKey ge ' 000100 ') och (RowKey Le ' 000199 ')  
* Om du vill hitta alla anställda på försäljnings avdelningen med en e-postadress som börjar med "a", sorterade i e-postadressen, använder du: $filter = (PartitionKey EQ email_Sales) och (RowKey ge "a") och (RowKey lt ' b ')  

Observera att filter syntaxen som används i föregående exempel är från tabellen Storage REST API. Mer information finns i [fråga entiteter](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du kan behålla dina dubbla entiteter konsekvent med varandra med hjälp av den [konsekvent transaktioner mönstret](#eventually-consistent-transactions-pattern) att underhålla de primära och sekundära index entiteterna.  
* Table Storage är relativt billigt att använda, så kostnads belastningen för lagring av dubblettdata bör inte vara en stor angelägenhet. Du kan dock alltid utvärdera kostnaden för din design baserat på dina förväntade lagrings krav och bara lägga till dubbla entiteter som stöder de frågor som klient programmet ska köra.  
* Värdet som används för `RowKey` måste vara unikt för varje entitet. Överväg att använda sammansatt nyckelvärden.  
* Utfyllnad av numeriska värden i `RowKey` (till exempel anställnings-ID 000223) möjliggör korrekt sortering och filtrering baserat på övre och nedre gränser.  
* Du behöver inte duplicera alla egenskaper för entiteten. Till exempel, om frågorna som söker efter entiteter med hjälp av e-postadressen i `RowKey` aldrig behöver den anställdas ålder, kan dessa entiteter ha följande struktur:
  
  ![Bild som visar anställd entitet med sekundärt index][11]
* Normalt är det bättre att lagra dubblettdata och se till att du kan hämta alla data du behöver med en enda fråga, än att använda en fråga för att hitta en entitet med hjälp av det sekundära indexet och en annan för att leta upp nödvändiga data i det primära indexet.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret i sådana här scenarier:

- Ditt klient program måste hämta entiteter genom att använda en mängd olika nycklar.
- Klienten måste hämta entiteter i olika sorterings ordningar.
- Du kan identifiera varje entitet genom att använda en mängd olika unika värden.

Använd det här mönstret om du vill undvika att överskrida gränserna för skalbarhet när du utför enhets ökningar genom att använda de olika `RowKey` värdena.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Konsekvent transaktioner mönster](#eventually-consistent-transactions-pattern)  
* [Mönster för Intra-partition sekundärt index](#intra-partition-secondary-index-pattern)  
* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Enhets grupp transaktioner](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Konsekvent transaktioner mönster
Aktivera konsekvent beteenden över partitionsgränser eller gränser för storage-system med hjälp av Azure-köer.  

#### <a name="context-and-problem"></a>Kontext och problem
EGTs aktivera atomiska transaktioner över flera enheter som delar samma partitionsnyckel. Av prestanda-och skalbarhets skäl kan du välja att lagra entiteter som har konsekvens krav i separata partitioner eller i ett separat lagrings system. I ett sådant scenario kan du inte använda EGTs för att upprätthålla konsekvens. Du kan till exempel ha ett krav att upprätthålla konsekvens mellan:  

* Entiteter som lagras i två olika partitioner i samma tabell, i olika tabeller eller i olika lagringskonton.  
* En entitet lagrad i Table Storage och en blob som lagras i Blob Storage.  
* En entitet lagrad i Table Storage och en fil i ett fil system.  
* En entitet lagrad i Table Storage, som har indexerats med Azure Kognitiv sökning.  

#### <a name="solution"></a>Lösning
Du kan implementera en lösning som ger konsekvens mellan två eller flera partitioner eller lagringssystem med hjälp av Azure-köer.

För att illustrera den här metoden förutsätter vi att du har ett krav för att kunna arkivera tidigare anställdas entiteter. De tidigare medarbetarnas entiteter frågas sällan och ska undantas från aktiviteter som hanterar aktuella anställda. För att implementera det här kravet lagrar du aktiva medarbetare i den **aktuella** tabellen och tidigare anställda i tabellen **Arkiv** . Genom att arkivera en medarbetare måste du ta bort entiteten från den **aktuella** tabellen och lägga till entiteten i tabellen **Arkiv** .

Men du kan inte använda en EGT för att utföra dessa två åtgärder. För att undvika risken för att ett fel gör en entitet som ska visas i båda eller ingen tabeller, måste arkivåtgärden vara konsekvent. Följande sekvensdiagram illustrerar stegen i den här åtgärden.  

![Diagrammet för slutlig konsekvens][12]

En klient initierar Arkiv åtgärden genom att placera ett meddelande i en Azure-kö (i det här exemplet för att arkivera medarbetar #456). En arbetsroll söker i kön för nya meddelanden. När den hittar en läser meddelandet och lämnar en dold kopia för kön. Arbetsrollen bredvid hämtar en kopia av entiteten från den **aktuella** tabellen, infogar en kopia i den **Arkiv** tabellen och tar sedan bort ursprungligt från den **aktuella** tabell. Slutligen, om det finns några fel från föregående steg, arbetsrollen tar bort dolda meddelandet från kön.  

I det här exemplet infogar steg 4 i diagrammet medarbetaren i **Arkiv** tabellen. Den kan lägga till medarbetaren i en BLOB i blob-lagring eller en fil i ett fil system.  

#### <a name="recover-from-failures"></a>Återställa från fel
Det är viktigt att åtgärderna i steg 4-5 i diagrammet blir *idempotenta* , om arbets rollen behöver starta om lagrings åtgärden. Om du använder Table Storage, för steg 4, bör du använda en "Infoga eller Ersätt"-åtgärd. i steg 5 bör du använda åtgärden "ta bort om finns" i klient biblioteket som du använder. Om du använder ett annat lagrings system måste du använda en lämplig idempotenta-åtgärd.  

Om arbets rollen aldrig Slutför steg 6 i diagrammet visas meddelandet igen i kön redo för arbets rollen för att försöka att bearbeta det igen efter en tids gräns. Arbets rollen kan kontrol lera hur många gånger ett meddelande i kön har lästs och, om det behövs flagga det som ett "Poison"-meddelande för undersökning genom att skicka det till en separat kö. Mer information om hur du läser Kömeddelanden och kontrollerar antalet ur kön finns i [Hämta meddelanden](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Vissa fel från Table Storage och Queue Storage är tillfälliga fel och klient programmet bör inkludera lämplig omprövnings logik för att hantera dem.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Den här lösningen tillhandahåller inte transaktions isolering. En-klient kan till exempel läsa **aktuella** och **arkivera** tabeller när arbets rollen var mellan steg 4-5 i diagrammet och se en inkonsekvent vy över data. Data kommer att vara konsekvent så småningom.  
* Du måste vara säker på att steg 4-5 är idempotenta för att säkerställa eventuell konsekvens.  
* Du kan skala lösningen med hjälp av flera köer och worker-rollinstanser.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill att garantera konsekvens mellan entiteter som finns i olika partitioner eller tabeller. Du kan utöka det här mönstret för att säkerställa eventuell konsekvens för åtgärder i Table Storage och Blob Storage och andra icke-Azure Storage data källor, till exempel en databas eller fil systemet.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Enhets grupp transaktioner](#entity-group-transactions)  
* [Sammanfoga eller ersätta](#merge-or-replace)  

> [!NOTE]
> Om transaktions isolering är viktigt för din lösning bör du överväga att designa om tabellerna så att du kan använda EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Mönster för index entiteter
Underhålla index entiteter för att aktivera effektiv sökning som returnerar en lista över entiteter.  

#### <a name="context-and-problem"></a>Kontext och problem
Table Storage indexerar automatiskt entiteter med hjälp av `PartitionKey` och `RowKey` värden. Detta gör att ett klient program kan hämta en entitet effektivt genom att använda en punkt fråga. Om du till exempel använder följande tabell struktur kan ett klient program effektivt hämta en enskild anställd entitet med hjälp av avdelnings namnet och anställnings-ID: t (`PartitionKey` och `RowKey`).  

![Bild av entiteten personal][13]

Om du också vill kunna hämta en lista över anställdas entiteter baserat på värdet för en annan icke-unik egenskap, till exempel efter namn, måste du använda en mindre effektiv partitions ökning. Den här sökningen söker efter matchningar i stället för att använda ett index för att se dem direkt. Detta beror på att Table Storage inte tillhandahåller sekundära index.  

#### <a name="solution"></a>Lösning
Om du vill aktivera sökning efter efter namn med föregående enhets struktur måste du underhålla listor med anställnings-ID. Om du vill hämta anställdas entiteter med ett visst efter namn, till exempel Johansson, måste du först leta upp listan med anställnings-ID för anställda med Johansson som efter namn och sedan hämta de anställdas entiteter. Det finns tre huvud alternativ för att lagra listor med anställnings-ID:  

* Använd Blob Storage.  
* Skapa index entiteter i samma partition som anställdas enheter.  
* Skapa index entiteter i en separat partition eller tabellen.  

Alternativ 1: Använd Blob Storage  

Skapa en BLOB för varje unikt efter namn och i varje BLOB lagrar en lista över `PartitionKey` (avdelning) och `RowKey` (anställnings-ID) för anställda som har det efter namnet. När du lägger till eller tar bort en medarbetare måste du kontrol lera att innehållet i den relevanta blobben är konsekvent med de anställdas entiteter.  

Alternativ 2: skapa index enheter i samma partition  

Använd indexerade entiteter som lagrar följande data:  

![Bild som visar en anställds entitet, med en sträng som innehåller en lista med anställnings-ID med samma efter namn][14]

Egenskapen `EmployeeIDs` innehåller en lista med anställnings-ID för anställda med efter namnet som lagras i `RowKey`.  

Följande steg beskriver processen som du bör följa när du lägger till en ny medarbetare. I det här exemplet ska vi lägga till en anställd med ID 000152 och efter namn Jones på försäljnings avdelningen:  

1. Hämta entiteten index med `PartitionKey` värde "Sales" och `RowKey` värdet "Johansson". Spara ETag för den här entiteten som ska användas i steg 2.  
2. Skapa en enhets grupp transaktion (det vill säga en batch-åtgärd) som infogar den nya personal enheten (`PartitionKey` värdet "försäljning" och `RowKey` värdet "000152") och uppdaterar index-entiteten (`PartitionKey` värde "Sales" och `RowKey` värdet "Johansson"). EGT gör detta genom att lägga till det nya medarbetar-ID: t i listan i fältet EmployeeIDs. Mer information om EGTs finns i [enhets grupp transaktioner](#entity-group-transactions).  
3. Om den här inrättningen Miss lyckas på grund av ett optimistiskt samtidighets fel (det vill säga någon annan har ändrat entiteten index) måste du börja om steg 1.  

Du kan använda en liknande metod för att ta bort en medarbetare om du använder det andra alternativet. Att ändra en anställds efter namn är något mer komplicerat eftersom du måste köra en avformat som uppdaterar tre entiteter: den anställdas entitet, index-entiteten för det gamla efter namnet och entiteten index för det nya efter namnet. Du måste hämta varje entitet innan du gör några ändringar, för att kunna hämta de ETag-värden som du sedan kan använda för att utföra uppdateringarna med hjälp av optimistisk samtidighet.  

Följande steg beskriver processen som du bör följa när du behöver leta upp alla anställda med ett visst efter namn på en avdelning. I det här exemplet ska vi leta upp alla anställda med efter namn Jones på försäljnings avdelningen:  

1. Hämta entiteten index med `PartitionKey` värde "Sales" och `RowKey` värdet "Johansson".  
2. Parsa listan med anställnings-ID: n i fältet `EmployeeIDs`.  
3. Om du behöver ytterligare information om var och en av dessa anställda (till exempel deras e-postadresser) kan du hämta var och en av de anställdas enheter genom att använda `PartitionKey` värde "försäljning" och `RowKey` värden från listan över medarbetare som du fick i steg 2.  

Alternativ 3: skapa index enheter i en separat partition eller tabell  

För det här alternativet använder du indexerade entiteter som lagrar följande data:  

![Bild som visar en anställds entitet, med en sträng som innehåller en lista med anställnings-ID med samma efter namn][15]

Egenskapen `EmployeeIDs` innehåller en lista med anställnings-ID för anställda med efter namnet som lagras i `RowKey`.  

Du kan inte använda EGTs för att upprätthålla konsekvens eftersom index entiteterna finns i en separat partition från de anställdas entiteter. Se till att index entiteterna är konsekventa med de anställdas entiteter.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Den här lösningen kräver minst två frågor för att hämta matchande entiteter: en för att skicka en fråga till indexerade entiteter för att hämta listan över `RowKey` värden och sedan frågor för att hämta varje entitet i listan.  
* Eftersom en enskild entitet har en maximal storlek på 1 MB, alternativ 2 och alternativ 3 i lösningen förutsätter vi att listan med anställnings-ID: n för ett visst efter namn aldrig är mer än 1 MB. Om listan över anställnings-ID sannolikt är större än 1 MB, använder du alternativ 1 och lagrar index data i Blob Storage.  
* Om du använder alternativ 2 (med EGTs för att hantera tillägg och radering av medarbetare, och om du ändrar en anställds efter namn), måste du utvärdera om volymen för transaktionerna ska närma sig skalbarhets gränserna i en viss partition. I så fall bör du överväga en konsekvent lösning (alternativ 1 eller alternativ 3). Dessa använder köer för att hantera uppdaterings begär Anden och gör att du kan lagra dina index enheter i en separat partition från entiteterna anställda.  
* Alternativ 2 i den här lösningen förutsätter att du vill leta efter efter namn på en avdelning. Till exempel vill du hämta en lista över anställda med efter namn Jones på försäljnings avdelningen. Om du vill kunna söka efter alla anställda med efter namn Jones i hela organisationen, använder du antingen alternativ 1 eller alternativ 3.
* Du kan implementera en Queue-baserad lösning som ger eventuell konsekvens. Mer information finns i mönster för senare [konsekventa transaktioner](#eventually-consistent-transactions-pattern).  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill leta upp en uppsättning entiteter som alla delar ett gemensamt egenskaps värde, till exempel alla anställda med efter namn Jones.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Konsekvent transaktioner mönster](#eventually-consistent-transactions-pattern)  
* [Enhets grupp transaktioner](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Denormalisering mönster
Kombinera relaterade data tillsammans i en enda enhet så att du kan hämta alla data som du behöver med en enda fråga.  

#### <a name="context-and-problem"></a>Kontext och problem
I en Relations databas normaliserar du vanligt vis data för att ta bort dubbletter som inträffar när frågor hämtar data från flera tabeller. Om du normalisera data i Azure-tabeller, måste du se flera kommunikationsturer från klienten till servern för att hämta relaterade data. Med följande tabell struktur behöver du till exempel två rund turer för att hämta information om en avdelning. En resa hämtar avdelnings enheten som innehåller chefens ID, och den andra resan hämtar chefens information i en anställds entitet.  

![Bild av avdelnings enhet och entitet för anställd][16]

#### <a name="solution"></a>Lösning
I stället för att lagra data i två separata entiteter, avnormalisera data och behålla en kopia av chefens information i entiteten avdelning. Ett exempel:  

![Bild av avnormaliserad och kombinerad avdelnings enhet][17]

Med avdelnings enheter lagrade med dessa egenskaper kan du nu hämta all information du behöver om en avdelning genom att använda en punkt fråga.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Det finns en kostnad som associeras med att lagra vissa data två gånger. Prestanda förmånen på grund av färre förfrågningar till Table Storage ger vanligt vis marginal ökning i lagrings kostnaderna. Vidare motbokas denna kostnad delvis genom en minskning av antalet transaktioner som du behöver för att hämta information om en avdelning.  
* Du måste ha två entiteter som lagrar information om hanterare av konsekvens. Du kan hantera konsekvens problemet genom att använda EGTs för att uppdatera flera entiteter i en enda Atomic-transaktion. I det här fallet lagras avdelnings enheten och den anställdas enhet för avdelnings chefen i samma partition.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du ofta behöva leta upp relaterad information. Det här mönstret minskar antalet frågor som din klient måste göra för att hämta de data som krävs.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Enhets grupp transaktioner](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Sammansatt nyckel mönster
Använd sammansatta `RowKey` värden om du vill att en klient ska kunna söka efter relaterade data med en enda punkt fråga.  

#### <a name="context-and-problem"></a>Kontext och problem
I en Relations databas är det naturligt att använda kopplingar i frågor för att returnera relaterade data delar till klienten i en enskild fråga. Du kan till exempel använda medarbetar-ID: t för att leta upp en lista över relaterade entiteter som innehåller prestanda och granska data för den anställda.  

Anta att du lagrar personal enheter i Table Storage med hjälp av följande struktur:  

![Bild av entiteten personal][18]

Du måste också lagra historiska data som rör granskningar och prestanda för varje år som den anställde har arbetat för din organisation och du måste kunna komma åt den här informationen per år. Ett alternativ är att skapa en annan tabell som lagrar entiteter med följande struktur:  

![Bild av entiteten för personal granskning][19]

Med den här metoden kan du välja att duplicera viss information (till exempel förnamn och efter namn) i den nya entiteten så att du kan hämta dina data med en enda begäran. Du kan dock inte upprätthålla stark konsekvens eftersom du inte kan använda en avkonsistens för att uppdatera de två entiteterna.  

#### <a name="solution"></a>Lösning
Lagra en ny entitetstyp i den ursprungliga tabellen genom att använda entiteter med följande struktur:  

![Bild av anställd entitet med sammansatt nyckel][20]

Observera hur `RowKey` nu är en sammansatt nyckel, som består av medarbetar-ID och året för gransknings data. På så sätt kan du hämta den anställdas prestanda och granska data med en enda begäran om en enda enhet.  

I följande exempel beskrivs hur du kan hämta alla granska data för en viss medarbetare (till exempel medarbetare 000123 på försäljningsavdelningen):  

$filter = (PartitionKey eq ”försäljning”) och (RowKey ge ”empid_000123”) och (RowKey lt 'empid_000124') & $select = RowKey, Arbetsledarens, Peer omdöme, kommentarer  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du bör använda ett lämpligt avgränsnings tecken som gör det enkelt att parsa `RowKey` värde: till exempel **000123_2012**.  
* Du lagrar även den här entiteten i samma partition som andra entiteter som innehåller relaterade data för samma anställd. Det innebär att du kan använda EGTs för att upprätthålla stark konsekvens.
* Du bör fundera över hur ofta du ska fråga data för att avgöra om det här mönstret är lämpligt. Om du till exempel får åtkomst till gransknings data och data för huvud anställda ofta, bör du behålla dem som separata entiteter.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra en eller flera relaterade entiteter som du frågar ofta.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Enhets grupp transaktioner](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#work-with-heterogeneous-entity-types)  
* [Konsekvent transaktioner mönster](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Mönster för log tail
Hämta de *n* entiteter som senast har lagts till i en partition med hjälp av ett `RowKey` värde som sorterar i omvänd datum-och tids ordning.  

> [!NOTE]
> Frågeresultat som returneras av Azure-Tabell-API i Azure Cosmos DB sorteras inte efter partitionsnyckel eller rad nyckel. Detta mönster är därför lämpligt för Table Storage, men det är inte lämpligt för Azure Cosmos DB. En detaljerad lista över funktions skillnader finns i [skillnader mellan tabell-API i Azure Cosmos DB och Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

#### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt krav är att kunna hämta de senast skapade entiteterna, till exempel de senaste tio utgifter ansökningar som görs av en medarbetare. Tabell frågor stöder en `$top` Query-åtgärd för att returnera de första *n* entiteterna från en mängd. Det finns ingen motsvarande fråga för att returnera de sista *n* entiteterna i en mängd.  

#### <a name="solution"></a>Lösning
Lagra entiteterna med hjälp av en `RowKey` som naturligt sorterar i omvänd datum-/tids ordning, så att den senaste posten alltid är den första i tabellen.  

Du kan till exempel använda en omvänd skalstreck-värde som härletts från aktuellt datum och tid för att kunna hämta de tio senaste utgiftsanspråk som skickats av en medarbetare. I följande C# kod exempel visas ett sätt att skapa ett lämpligt "inverterade skal streck"-värde för en `RowKey` som sorterar från den senaste till det äldsta:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Du kan gå tillbaka till datum/tid-värdet med hjälp av följande kod:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Tabell-fråga som ser ut så här:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du måste fylla i det omvända skalet med inledande nollor för att se till att strängvärdet sorteras som förväntat.  
* Du måste känna till det för skalbarhetsmål på nivån för en partition. Var noga med att inte skapa varma dekor partitioner.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver åtkomst till entiteter i omvänd datum-/tids ordning, eller när du behöver åtkomst till de entiteter som du nyligen lagt till.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Lägg till åtkomstgruppen / lägga till ett mönster](#prepend-append-anti-pattern)  
* [Hämta entiteter](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>Mönster för hög volym delete
Aktivera borttagning av en stor mängd entiteter genom att lagra alla entiteter för samtidig borttagning i en egen separat tabell. Du tar bort entiteterna genom att ta bort tabellen.  

#### <a name="context-and-problem"></a>Kontext och problem
Många program ta bort gamla data som behöver inte längre ska vara tillgängliga för ett klientprogram eller som programmet har arkiverats till ett annat lagringsmedium. Du identifierar vanligt vis sådana data med ett datum. Du kan till exempel ha ett krav för att ta bort poster för alla inloggnings begär Anden som är mer än 60 dagar gamla.  

En möjlig design är att använda datum och tid för inloggnings förfrågan i `RowKey`:  

![Bild av entiteten för inloggnings försök][21]

Med den här metoden undviker du klickbara områden för partitioner, eftersom programmet kan infoga och ta bort inloggnings enheter för varje användare i en separat partition. Den här metoden kan dock vara kostsam och tids krävande om du har ett stort antal entiteter. Först måste du utföra en tabells ökning för att kunna identifiera alla entiteter som ska tas bort och sedan måste du ta bort varje gammal entitet. Du kan minska antalet sändningar till servern som krävs för att ta bort de gamla enheterna med batchbearbetning flera delete-begäranden till EGTs.  

#### <a name="solution"></a>Lösning
Använd en separat tabell för varje försök att logga in. Du kan använda föregående enhets design för att undvika hotspots när du lägger till entiteter. Att ta bort gamla entiteter är nu bara en fråga om att ta bort en tabell varje dag (en enda lagrings åtgärd), i stället för att hitta och ta bort hundratals och tusentals enskilda inloggnings enheter varje dag.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Stöder din design andra sätt att använda data, till exempel leta upp specifika entiteter, länka till andra data eller generera sammanställd information?  
* Din design undviker aktiva punkter när du infogar nya entiteter?  
* Förvänta dig en fördröjning om du vill återanvända samma namn när du tar bort den. Är det bättre att alltid använda unikt tabellnamn.  
* En viss hastighets begränsning när du först använder en ny tabell, medan Table Storage lär sig åtkomst mönstren och distribuerar partitionerna över noder. Du bör överväga hur ofta du behöver skapa nya tabeller.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du har ett stort antal entiteter som du måste ta bort samtidigt.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Enhets grupp transaktioner](#entity-group-transactions)
* [Ändra entiteter](#modify-entities)  

### <a name="data-series-pattern"></a>Serien datamönster
Store fullständig dataserier i en enda entitet att minimera antalet begäranden som du gör.  

#### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt scenario är för ett program för att lagra en serie med data som vanligtvis krävs för att hämta allt samtidigt. Ditt program kan till exempel registrera hur många IM meddelanden varje medarbetare skickar varje timme och sedan använda informationen för att rita ut hur många meddelanden varje användare som skickas över föregående 24 timmar. En design kan vara att lagra 24 entiteter för varje medarbetare:  

![Bild av entiteten Message statistik][22]

Med den här designen kan du enkelt hitta och uppdatera enheten att uppdatera för varje medarbetare när programmet måste uppdatera värdet för antal meddelande. Men om du vill hämta information för att rita ett diagram för aktiviteten i föregående 24 timmar, måste du hämta 24 entiteter.  

#### <a name="solution"></a>Lösning
Använd följande design, med en separat egenskap för att lagra antalet meddelanden i varje timme:  

![Bild som visar entiteten Message statistik med avgränsade egenskaper][23]

Du kan använda en sammanfogning med den här designen för att uppdatera meddelandeantalet för en anställd för en viss timme. Nu kan du hämta all information som du behöver för att rita diagrammet genom att använda en begäran för en enda entitet.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Om din fullständiga data serie inte passar in i en enskild entitet (en entitet kan ha upp till 252 egenskaper) använder du ett alternativt data lager, till exempel en blob.  
* Om du har flera klienter som uppdaterar en entitet samtidigt använder du **etag** för att implementera optimistisk samtidighet. Om du har många klienter kan du uppleva hög konkurrens.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill uppdatera och hämta en serie som är associerade med en enskild entitet.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Mönster för stora entiteter](#large-entities-pattern)  
* [Sammanfoga eller ersätta](#merge-or-replace)  
* [Eventuellt konsekventa transaktions mönster](#eventually-consistent-transactions-pattern) (om du lagrar data serien i en BLOB)  

### <a name="wide-entities-pattern"></a>Mönster för många entiteter
Använda flera fysiska enheter för att lagra logiska entiteter med mer än 252 egenskaper.  

#### <a name="context-and-problem"></a>Kontext och problem
En enskild entitet får ha högst 252 egenskaper (exklusive de obligatoriska system egenskaperna) och kan inte lagra mer än 1 MB data totalt. I en Relations databas kan du vanligt vis undvika gränser för storleken på en rad genom att lägga till en ny tabell och tvinga en 1-till-1-relation mellan dem.  

#### <a name="solution"></a>Lösning
Genom att använda Table Storage kan du lagra flera entiteter som representerar ett enda stort företags objekt med fler än 252 egenskaper. Om du till exempel vill lagra antalet snabb meddelanden som skickats av varje medarbetare under de senaste 365 dagarna kan du använda följande design som använder två entiteter med olika scheman:  

![Bild som visar Message stats-entiteten med Rowkey 01-och Message stats-entiteten med Rowkey 02][24]

Om du behöver göra en ändring som behöver uppdateras både entiteter för att de alltid är synkroniserade med varandra kan använda du en EGT. Annars kan använda du en enda merge-operation för att uppdatera meddelandeantalet för en viss dag. Om du vill hämta alla data för en enskild medarbetare måste du hämta båda entiteterna. Du kan göra detta med två effektiva begär Anden som använder både ett `PartitionKey` och ett `RowKey` värde.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur det här mönstret ska implementeras:  

* Hämta en fullständig logisk entitet innebär att minst två lagringstransaktioner: en för att hämta varje fysisk entitet.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra entiteter vars storlek eller antal egenskaper överskrider gränserna för en enskild entitet i Table Storage.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Enhets grupp transaktioner](#entity-group-transactions)
* [Sammanfoga eller ersätta](#merge-or-replace)

### <a name="large-entities-pattern"></a>Mönster för stora entiteter
Använd Blob Storage för att lagra stora egenskaps värden.  

#### <a name="context-and-problem"></a>Kontext och problem
En enskild entitet kan inte lagra mer än 1 MB data totalt. Om en eller flera av dina egenskaper lagrar värden som leder till att den totala storleken på din entitet överskrider det här värdet kan du inte lagra hela entiteten i Table Storage.  

#### <a name="solution"></a>Lösning
Om din enhet överskrider 1 MB storlek eftersom en eller flera egenskaper innehåller stora mängder data, kan du lagra data i Blob Storage och sedan lagra adressen för blobben i en egenskap i entiteten. Du kan till exempel lagra fotot av en medarbetare i Blob Storage och lagra en länk till fotot i egenskapen `Photo` för din anställdas entitet:  

![Bild som visar den anställda entiteten med en sträng för foto som pekar på Blob Storage][25]

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Om du vill upprätthålla eventuell konsekvens mellan entiteten i Table Storage och data i blob-lagring använder du det senare, [konsekventa transaktions mönstret](#eventually-consistent-transactions-pattern) för att underhålla dina entiteter.
* Hämta en fullständig entitet innebär att minst två lagringstransaktioner: en för att hämta entiteten och en för att hämta blob-data.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra entiteter vars storlek överskrider gränserna för en enskild entitet i Table Storage.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Konsekvent transaktioner mönster](#eventually-consistent-transactions-pattern)  
* [Mönster för många entiteter](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Lägg till åtkomstgruppen/lägga till ett mönster
När du har en stor mängd med infogningar ökar du skalbarheten genom att sprida infogningarna över flera partitioner.  

#### <a name="context-and-problem"></a>Kontext och problem
Prepending eller lägger till entiteter i dina entiteter för lagrade vanligtvis resulterar i programmet att lägga till nya entiteter i den första eller sista partitionen i en sekvens av partitioner. I det här fallet sker alla infogningar vid en viss tidpunkt i samma partition, vilket skapar en hotspot. Detta förhindrar att tabell lagring från belastnings utjämning infogas över flera noder och kan leda till att ditt program når skalbarhets målen för partition. Anta till exempel fallet för ett program som loggar nätverks-och resurs åtkomst av anställda. En enhets struktur, till exempel följande kan resultera i att den aktuella timmens partition blir ett hotspot-område, om mängden transaktioner når skalbarhets målet för en enskild partition:  

![Bild av entiteten personal][26]

#### <a name="solution"></a>Lösning
Följande alternativa Entity-struktur förhindrar en hotspot för en viss partition, eftersom program loggar händelser:  

![Bild som visar den anställda entiteten med RowKey som sammansatta året, månaden, dagen, timmen och händelse-ID][27]

Observera i det här exemplet hur både `PartitionKey` och `RowKey` sammansatta nycklar. `PartitionKey` använder både avdelnings-och medarbetar-ID för att distribuera loggningen över flera partitioner.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Stöder den alternativa nyckelstruktur som undviker skapa heta partitioner på infogningar effektivt frågorna klientprogrammet gör?  
* Innebär den förväntade volymen av transaktioner att du förmodligen når skalbarhets målen för en enskild partition och att den begränsas av tabell lagringen?  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Undvik lägga/Lägg till anti-mönstret när din volym av transaktioner sannolikt kommer att leda till en hastighet som begränsars av Table Storage när du ansluter till en aktiv partition.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan vara relevanta när du implementerar det här mönstret:  

* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Mönster för log tail](#log-tail-pattern)  
* [Ändra entiteter](#modify-entities)  

### <a name="log-data-anti-pattern"></a>Logga ett datamönster
Normalt bör du använda Blob Storage i stället för Table Storage för att lagra loggdata.  

#### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt användnings fall för loggdata är att hämta ett urval av logg poster för ett visst datum/tidsintervall. Till exempel vill du hitta alla fel och viktiga meddelanden som ditt program loggat in mellan 15:04 och 15:06 på ett visst datum. Du vill inte använda datum och tid för logg meddelandet för att bestämma vilken partition som du sparar log-entiteter på. Detta resulterar i en aktiv partition eftersom alla log-entiteter delar samma `PartitionKey`-värde (se [lägga/append Anti-pattern](#prepend-append-anti-pattern)). Följande enhets schema för ett logg meddelande resulterar till exempel i en aktiv partition, eftersom programmet skriver alla logg meddelanden till partitionen för aktuellt datum och timma:  

![Bild av entiteten logg meddelande][28]

I det här exemplet innehåller `RowKey` datum och tid för logg meddelandet för att säkerställa att logg meddelanden sorteras i datum-/tids ordning. `RowKey` innehåller också ett meddelande-ID, om flera logg meddelanden delar samma datum och tid.  

En annan metod är att använda en `PartitionKey` som garanterar att programmet skriver meddelanden över flera olika partitioner. Om källan till logg meddelandet till exempel är ett sätt att distribuera meddelanden över flera partitioner, kan du använda följande enhets schema:  

![Bild av entiteten logg meddelande][29]

Problemet med det här schemat är dock att om du vill hämta alla logg meddelanden för ett särskilt tidsintervall måste du söka igenom varje partition i tabellen.

#### <a name="solution"></a>Lösning
I föregående avsnitt har du markerat problemet med att försöka använda Table Storage för att lagra logg poster och föreslagna två otillfredsställande design. En lösning ledde till en aktiv partition med risken för dåliga prestanda vid skrivning av logg meddelanden. Den andra lösningen resulterade i dåliga frågeresultat, på grund av kravet på att söka igenom varje partition i tabellen för att hämta logg meddelanden för ett särskilt tidsintervall. Blob Storage erbjuder en bättre lösning för den här typen av scenario och det här är hur Azure Storage analys lagrar loggdata som samlas in.  

Det här avsnittet beskriver hur lagrings analys lagrar loggdata i Blob Storage, som en illustration av den här metoden för att lagra data som du normalt frågar efter intervall.  

Storage Analytics lagrar logg meddelanden i avgränsat format i flera blobbar. Avgränsad format gör det enkelt för ett klientprogram att parsa data i loggmeddelandet.  

Storage Analytics använder en namngivnings konvention för blobbar som gör att du kan hitta bloben (eller blobbar) som innehåller de logg meddelanden som du söker efter. Till exempel innehåller en blob med namnet "Queue/2014/07/31/1800/000001. log" logg meddelanden som är relaterade till Queue Service för timmen från 18:00 den 31 juli 2014. ”000001” anger att detta är den första loggfilen för den här perioden. Storage Analytics registrerar även tidsstämplar för de första och sista logg meddelandena som lagras i filen, som en del av blobens metadata. Med API: et för Blob Storage kan du hitta blobbar i en behållare baserat på ett namn prefix. Du kan använda prefixet "kö/2014/07/31/1800" för att hitta alla blobbar som innehåller kös logg data för timmen från och med 18:00.  

Storage Analytics buffrar logg meddelanden internt och uppdaterar sedan regelbundet rätt BLOB eller skapar en ny med den senaste batchen logg poster. Detta minskar antalet skrivningar som måste utföras till Blob Storage.  

Om du implementerar en liknande lösning i ditt eget program bör du överväga hur du ska hantera kompromisser mellan pålitlighet och kostnad och skalbarhet. Med andra ord kan du utvärdera resultatet av att skriva varje loggpost till Blob Storage när det sker, jämfört med att buffra uppdateringar i programmet och skriva dem till Blob Storage i batchar.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du lagrar loggdata:  

* Om du skapar en tabell design som undviker eventuella varma partitioner, kanske du upptäcker att du inte kan komma åt dina data på ett effektivt sätt.  
* Om du vill bearbeta loggdata, en klient behöver ofta läsa in många poster.  
* Även om loggdata ofta är strukturerade kan Blob Storage vara en bättre lösning.  

### <a name="implementation-considerations"></a>Att tänka på vid implementering
Det här avsnittet beskrivs några saker att ha i åtanke när du implementerar de mönster som beskrivs i föregående avsnitt. De flesta av det här avsnittet använder exempel som skrivits i C# och som använder Storage-klientbibliotek (version 4.3.0 då skrivs).  

### <a name="retrieve-entities"></a>Hämta entiteter
Enligt beskrivningen i avsnittet [Design för att fråga](#design-for-querying), mest effektivt fråga är en punkt-fråga. I vissa fall kan du dock behöva hämta flera entiteter. I det här avsnittet beskrivs några vanliga metoder för att hämta entiteter med hjälp av lagrings klient biblioteket.  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>Köra en punkt fråga med hjälp av lagrings klient biblioteket
Det enklaste sättet att köra en punkt fråga är att använda åtgärden **Hämta** tabell. Som du ser i följande C# kodfragment hämtar den här åtgärden en entitet med en `PartitionKey` med värdet "Sales" och en `RowKey` med värdet "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Observera hur det här exemplet förväntar sig att entiteten hämtas till typ `EmployeeEntity`.  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>Hämta flera entiteter med hjälp av LINQ
Du kan hämta flera entiteter med hjälp av LINQ med lagrings klient biblioteket och ange en fråga med en **WHERE** -sats. För att undvika en tabells ökning bör du alltid inkludera `PartitionKey`-värdet i WHERE-satsen och om möjligt `RowKey` svärdet för att undvika genomsökningar i tabeller och partitioner. Table Storage stöder en begränsad uppsättning jämförelse operatorer (större än, större än eller lika med, mindre än, mindre än eller lika med, lika med och inte lika med) som ska användas i WHERE-satsen. Följande C# kodfragment söker efter alla anställda vars efter namn börjar med "B" (förutsatt att `RowKey` lagrar efter namn) på försäljnings avdelningen (förutsatt att `PartitionKey` lagrar avdelnings namnet):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Observera hur frågan anger både en `RowKey` och en `PartitionKey` för att säkerställa bättre prestanda.  

Följande kod exempel visar likvärdiga funktioner med hjälp av Fluent-API: et (mer information om Fluent-API: er i allmänhet finns i [metod tips för att utforma en Fluent-API](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

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
> Exemplet kapslar flera `CombineFilters` metoder för att inkludera de tre filter villkoren.  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>Hämta ett stort antal entiteter från en fråga
En optimal fråga returnerar en enskild entitet baserat på ett `PartitionKey` värde och ett `RowKey` värde. I vissa fall kan du dock ha ett krav för att returnera många entiteter från samma partition, eller till och med från flera partitioner. I sådana situationer bör du alltid helt testa programmets prestanda.  

En fråga mot Table Storage kan returnera högst 1 000 entiteter vid ett tillfälle och köras i högst fem sekunder. Table Storage returnerar en tilläggs-token som gör att klient programmet kan begära nästa uppsättning entiteter, om något av följande stämmer:

- Resultat uppsättningen innehåller över 1 000 entiteter.
- Frågan slutfördes inte inom fem sekunder.
- Frågan korsar partitionens gränser. 

Mer information om hur du använder fortsättnings-token finns i [fråga om tids gräns och sid brytning](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Om du använder lagrings klient biblioteket kan du automatiskt hantera tilläggs-token åt dig när det returnerar entiteter från Table Storage. Exempel: följande C# kod exempel hanterar automatiskt fortsättnings-token om Table Storage returnerar dem i ett svar:  

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

Du kan styra när programmet hämtar data nästa segment med hjälp av fortsättning token uttryckligen. Om ditt klient program till exempel gör det möjligt för användarna att gå igenom de entiteter som lagras i en tabell, kan en användare välja att inte gå igenom alla entiteter som hämtas av frågan. Ditt program använder bara en fortsättnings-token för att hämta nästa segment när användaren hade avslutat växlingen genom alla entiteter i det aktuella segmentet. Den här metoden har flera fördelar:  

* Du kan begränsa mängden data som hämtas från tabell lagring och som du flyttar över nätverket.  
* Du kan utföra asynkron i/O i .NET.  
* Du kan serialisera över gångs-token till beständig lagring, så att du kan fortsätta i händelse av en program krasch.  

> [!NOTE]
> En fortsättnings-token returnerar vanligt vis ett segment som innehåller 1 000 entiteter, även om det kan innehålla färre. Detta är också fallet om du begränsar antalet poster som en fråga returnerar genom att använda **ta** för att returnera de första n entiteter som matchar Sök kriterierna. Table Storage kan returnera ett segment som innehåller färre än n entiteter, tillsammans med en fortsättnings-token så att du kan hämta återstående entiteter.  
> 
> 

Följande C#-kod visar hur du ändrar antalet enheter som returneras i ett segment:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projektion för serversidan
En enda entitet kan ha upp till 255 egenskaper och upp till 1 MB i storlek. När du frågar tabellen och hämtar entiteter kanske du inte behöver alla egenskaper, och du kan undvika att överföra data i onödan (för att minska svars tid och kostnader). Du kan använda serversidan projektion Överför bara de egenskaper som du behöver. I följande exempel hämtas bara `Email`-egenskapen (tillsammans med `PartitionKey`, `RowKey`, `Timestamp`och `ETag`) från entiteterna som valts av frågan.  

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

Observera hur `RowKey`-värdet är tillgängligt även om det inte finns med i listan över egenskaper som ska hämtas.  

### <a name="modify-entities"></a>Ändrar entiteter
Med lagrings klient biblioteket kan du ändra dina entiteter som lagras i Table Storage genom att infoga, ta bort och uppdatera entiteter. Du kan använda EGTs för att utföra flera infognings-, uppdaterings-och borttagnings åtgärder tillsammans för att minska antalet fördröjningar som krävs och förbättra lösningens prestanda.  

Undantag som har utlösts när lagrings klient biblioteket kör en budget inkluderar vanligt vis indexet för den enhet som orsakade att batchen skulle krascha. Detta är användbart när du felsöker kod som använder EGTs.  

Du bör också överväga hur din design påverkar hanteringen av samtidighet och uppdateringsåtgärderna i klientprogrammet.  

#### <a name="managing-concurrency"></a>Hantera samtidighet
Som standard implementerar Table Storage optimistisk concurrency-kontroller på nivån för enskilda entiteter för INSERT-, merge-och Delete-åtgärder, även om det är möjligt för en klient att tvinga tabell lagring att kringgå kontrollerna. Mer information finns i [Hantera samtidighet i Microsoft Azure Storage](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Sammanfoga eller ersätta
Metoden `Replace` i `TableOperation`-klassen ersätter alltid den fullständiga entiteten i Table Storage. Om du inte tar med en egenskap i begäran när egenskapen finns i den lagrade entiteten tar begäran bort den egenskapen från den lagrade entiteten. Om du inte vill ta bort en egenskap uttryckligen från en lagrad entitet, måste du inkludera varje egenskap i begäran.  

Du kan använda metoden `Merge` i klassen `TableOperation` för att minska mängden data som du skickar till tabell lagring när du vill uppdatera en entitet. Metoden `Merge` ersätter alla egenskaper i den lagrade entiteten med egenskaps värden från entiteten som ingår i begäran. Den här metoden lämnar alla egenskaper i den lagrade entiteten som inte ingår i begäran. Detta är användbart om du har stora entiteter och bara behöver uppdatera ett litet antal egenskaper i en begäran.  

> [!NOTE]
> Metoderna `*Replace` och `Merge` fungerar inte om entiteten inte finns. Alternativt kan du använda `InsertOrReplace` och `InsertOrMerge` metoder som skapar en ny entitet om den inte finns.  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>Arbeta med heterogena entitetstyper
Table Storage är ett *schema löst* tabell lager. Det innebär att en enskild tabell kan lagra entiteter av flera typer, vilket ger stor flexibilitet i din design. I följande exempel visas en tabell som lagrar både anställda och avdelning entiteter:  

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

Varje entitet måste fortfarande ha `PartitionKey`, `RowKey`och `Timestamp` värden, men kan ha en uppsättning egenskaper. Det finns dessutom inget som anger typen av entitet om du inte väljer att lagra informationen någonstans. Det finns två alternativ för att identifiera entitetstypen:  

* Lägga av entitetstypen till `RowKey` (eller eventuellt `PartitionKey`). Till exempel `EMPLOYEE_000123` eller `DEPARTMENT_SALES` som `RowKey` värden.  
* Använd en separat egenskap för att registrera entitetstypen, som du ser i följande tabell.  

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
<th>DepartmentName</th>
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

Det första alternativet, beroende på enhets typ till `RowKey`, är användbart om det finns en möjlighet att två entiteter av olika typer kan ha samma nyckel värde. Den också grupperar entiteter av samma typ tillsammans i partitionen.  

De metoder som beskrivs i det här avsnittet är särskilt relevanta för diskussionen om[arvs relationer](#inheritance-relationships).  

> [!NOTE]
> Överväg att inkludera ett versions nummer i värdet för enhets typen för att göra det möjligt för klient program att utveckla POCO-objekt och arbeta med olika versioner.  
> 
> 

Resten av det här avsnittet beskrivs några av de funktioner i Storage-klientbiblioteket som underlättar arbetet med flera typer av enheter i samma tabell.  

#### <a name="retrieve-heterogeneous-entity-types"></a>Hämta typer av heterogena enheter
Om du använder lagrings klient biblioteket har du tre alternativ för att arbeta med flera olika entitetstyper.  

Om du vet vilken typ av entitet som lagras med vissa `RowKey` och `PartitionKey` värden kan du ange entitetstypen när du hämtar entiteten. Du såg detta i de föregående två exemplen som hämtar entiteter av typen `EmployeeEntity`: [kör en punkt fråga med hjälp av lagrings klient biblioteket](#run-a-point-query-by-using-the-storage-client-library) och [Hämta flera entiteter med hjälp av LINQ](#retrieve-multiple-entities-by-using-linq).  

Det andra alternativet är att använda `DynamicTableEntity` typ (en egenskaps uppsättning) i stället för en konkret POCO-entitetstyp. Det här alternativet kan också förbättra prestandan eftersom det inte behövs att serialisera och deserialisera entiteten till .NET-typer. Följande C# kod kan användas för att hämta flera entiteter av olika typer från tabellen, men returnerar alla entiteter som `DynamicTableEntity` instanser. Den använder sedan egenskapen `EntityType` för att fastställa typen av varje entitet:  

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

Om du vill hämta andra egenskaper måste du använda metoden `TryGetValue` i egenskapen `Properties` i `DynamicTableEntity`-klassen.  

Ett tredje alternativ är att kombinera med `DynamicTableEntity` typ och en `EntityResolver` instans. Detta gör det möjligt för dig att lösa flera POCO-typer i samma fråga. I det här exemplet använder det `EntityResolver` ombudet `EntityType`-egenskapen för att skilja mellan de två typer av entiteter som frågan returnerar. Metoden `Resolve` använder `resolver`-delegaten för att lösa `DynamicTableEntity` instanser för att `TableEntity` instanser.  

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

#### <a name="modify-heterogeneous-entity-types"></a>Ändra typer av heterogena enheter
Du behöver inte känna till typen av en entitet för att ta bort den och du vet alltid vilken typ av enhet som används när du infogar den. Du kan dock använda `DynamicTableEntity` typen för att uppdatera en entitet utan att känna till dess typ och utan att använda en POCO entitet-klass. I följande kod exempel hämtas en enskild entitet och kontrollerar att egenskapen `EmployeeCount` finns innan du uppdaterar den.  

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

### <a name="control-access-with-shared-access-signatures"></a>Kontrol lera åtkomst med signaturer för delad åtkomst
Du kan använda signaturer för delad åtkomst (SAS) för att aktivera att klient program ändrar (och frågar) tabell enheter direkt, utan att behöva autentisera direkt med Table Storage. Det finns vanligtvis tre huvudsakliga fördelar med SAS i ditt program:  

* Du behöver inte distribuera lagrings konto nyckeln till en osäker plattform (till exempel en mobil enhet) för att tillåta att enheten får åtkomst till och ändrar entiteter i Table Storage.  
* Du kan avlasta en del av arbetet som webb-och arbets roller utför i hantering av dina entiteter. Du kan avlasta till klient enheter som slut användar datorer och mobila enheter.  
* Du kan tilldela en begränsad och tidsbegränsad uppsättning behörigheter till en klient (till exempel tillåta skrivskyddad åtkomst till särskilda resurser).  

Mer information om hur du använder SAS-token med Table Storage finns i [använda signaturer för delad åtkomst (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Du måste dock fortfarande generera SAS-token som ger ett klient program till entiteterna i Table Storage. Gör detta i en miljö som har säker åtkomst till dina lagrings konto nycklar. Normalt använder du en web- eller worker-roll för att generera SAS-token och skicka dem till klientprogram som behöver åtkomst till dina entiteter. Eftersom det finns fortfarande en belastning i genererar och leverera SAS-token till klienter, bör du hur du bäst för att minska den här kostnader, särskilt i scenarion med stora volymer.  

Det går att skapa en SAS-token som beviljar åtkomst till en delmängd av entiteterna i en tabell. Som standard skapar du en SAS-token för en hel tabell. Men det är också möjligt att ange att SAS-token beviljar åtkomst till antingen ett intervall med `PartitionKey` värden eller ett intervall med `PartitionKey` och `RowKey` värden. Du kan välja att generera SAS-token för enskilda användare av systemet, så att varje användares SAS-token endast tillåter åtkomst till sina egna entiteter i Table Storage.  

### <a name="asynchronous-and-parallel-operations"></a>Asynkron och parallella åtgärder
Förutsatt att dina förfrågningar sprids över flera partitioner, kan du förbättra svarstiden för dataflöde och klienten med hjälp av asynkrona eller parallella frågor.
Du kan till exempel ha två eller flera arbetsrollinstanser åtkomst till dina tabeller parallellt. Du kan ha enskilda arbets roller som är ansvariga för specifika uppsättningar partitioner eller bara ha flera arbets Rolls instanser, och varje användare kan komma åt alla partitioner i en tabell.  

Inom en klient instans kan du förbättra data flödet genom att köra lagrings åtgärder asynkront. Storage-klientbiblioteket gör det enkelt att skriva asynkrona frågor och ändringar. Du kan till exempel börja med den synkrona metoden som hämtar alla entiteter i en partition, som du ser i följande C# kod:  

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

Du kan enkelt ändra koden så att frågan körs asynkront, enligt följande:  

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

* Metodsignaturen innehåller nu `async` modifierare och returnerar en `Task`-instans.  
* I stället för att anropa metoden `ExecuteSegmented` för att hämta resultat anropar metoden `ExecuteSegmentedAsync`-metoden. Metoden använder `await` modifieraren för att hämta resultatet asynkront.  

Klient programmet kan anropa den här metoden flera gånger, med olika värden för parametern `department`. Varje fråga körs i en separat tråd.  

Det finns ingen asynkron version av `Execute`-metoden i `TableQuery`-klassen, eftersom `IEnumerable` gränssnittet inte stöder asynkron uppräkning.  

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

Du kan enkelt ändra koden så att uppdateringen körs asynkront, enligt följande:  

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

* Metodsignaturen innehåller nu `async` modifierare och returnerar en `Task`-instans.  
* I stället för att anropa metoden `Execute` för att uppdatera entiteten anropar metoden `ExecuteAsync`-metoden. Metoden använder `await` modifieraren för att hämta resultatet asynkront.  

Klient programmet kan anropa flera asynkrona metoder som det här, och varje metod anrop körs i en separat tråd.  


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


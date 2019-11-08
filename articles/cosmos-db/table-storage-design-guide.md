---
title: Utforma Azure Cosmos DB tabeller för att stödja skalning och prestanda
description: 'Azure Storage tabell design guide: Utforma skalbara och presterande tabeller i Azure Cosmos DB och Azure Storage tabell'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: wmengmsft
ms.author: wmeng
ms.custom: seodec18
ms.openlocfilehash: 499ac3a394339ebb07c36abeaaa761de22927941
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827778"
---
# <a name="azure-storage-table-design-guide-designing-scalable-and-performant-tables"></a>Azure Storage tabell design guide: Utforma skalbara och presterande tabeller

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

För att utforma skalbara och presterande tabeller måste du fundera på ett antal faktorer som prestanda, skalbarhet och kostnad. Om du tidigare har utformat scheman för Relations databaser, är det viktigt att du vet att du, men det finns några likheter mellan Azure Table service Storage-modellen och Relations modeller, men det finns också många viktiga skillnader. Dessa skillnader leder vanligt vis till olika designer som kan se till att det är intuitivt eller fel i någon bekant relation mellan Relations databaser, men som är bra att känna till om du designar för en NoSQL nyckel/värde-lagring, till exempel Azure Table service. Många av dina design skillnader visar det faktum att Table service har utformats för att stödja molnbaserade program som kan innehålla miljarder entiteter (rader i Relations databas terminologi) eller för data uppsättningar som måste ha stöd för hög transaktion volymer: därför måste du tänka på olika sätt att lagra dina data och förstå hur Table service fungerar. Ett väl utformat NoSQL-datalager kan göra det möjligt för din lösning att skala mycket ytterligare (och en lägre kostnad) än en lösning som använder en Relations databas. Den här guiden hjälper dig med dessa ämnen.  

## <a name="about-the-azure-table-service"></a>Om Azure-Table service
I det här avsnittet beskrivs några av de viktigaste funktionerna i Table service som är särskilt relevanta för att utforma för prestanda och skalbarhet. Om du är nybörjare på Azure Storage och Table service kan du först läsa [Introduktion till Microsoft Azure Storage](../storage/common/storage-introduction.md) och [komma igång med Azure Table Storage med .net](table-storage-how-to-use-dotnet.md) innan du läser resten av den här artikeln. Även om den här hand boken är på Table service, innehåller den viss diskussion om Azure-kö-och blob-tjänster och hur du kan använda dem tillsammans med Table service i en lösning.  

Vad är Table service? Som du kan förväntar dig från namnet använder Table service ett tabell format för att lagra data. I standard terminologin representerar varje rad i tabellen en entitet och kolumnerna lagrar de olika egenskaperna för entiteten. Varje entitet har ett nyckel par för att unikt identifiera den och en tidsstämpelkolumn som Table service använder för att spåra när entiteten senast uppdaterades (tidsstämpel-fältet läggs till automatiskt och du kan inte skriva över tidsstämpeln manuellt med ett godtyckligt värde). Table service använder den senast ändrade tidsstämpeln (LMT) för att hantera optimistisk samtidighet.  

> [!NOTE]
> Table service REST API-åtgärder returnerar också ett **etag** -värde som den härleds från den senast ändrade tidsstämpeln (LMT). I det här dokumentet märker du termerna ETag och LMT så att de refererar till samma underliggande data.  
> 
> 

I följande exempel visas en enkel tabell design där du kan lagra personal-och avdelnings enheter. Många av exemplen som visas senare i den här guiden baseras på den här enkla designen.  

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
<td>Allt</td>
<td>Sal</td>
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
<td>Juni</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marknadsföring</td>
<td>Avdelning</td>
<td>2014-08-22T00:50:13.45.30 Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
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
<td>Sales</td>
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


Hittills ser den här designen ut ungefär som en tabell i en Relations databas med de viktigaste skillnaderna som de obligatoriska kolumnerna och möjligheten att lagra flera entitetstyper i samma tabell. Dessutom har var och en av de användardefinierade egenskaperna, till exempel **FirstName** eller **Age** , en datatyp, till exempel Integer eller string, precis som en kolumn i en Relations databas. Men till skillnad från i en Relations databas innebär att en egenskap som är mindre av Table service innebär att en egenskap inte behöver ha samma datatyp på varje entitet. Om du vill lagra komplexa data typer i en enskild egenskap måste du använda ett serialiserat format, till exempel JSON eller XML. Mer information om tabell tjänsten, till exempel data typer som stöds, datum intervall som stöds, namngivnings regler och storleks begränsningar, finns i [förstå tabell tjänstens data modell](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Som du ser är ditt val av **PartitionKey** och **RowKey** grundläggande för en bra tabell design. Varje entitet som lagras i en tabell måste ha en unik kombination av **PartitionKey** och **RowKey**. Precis som med nycklar i en Relations databas tabell indexeras värdena **PartitionKey** och **RowKey** för att skapa ett grupperat index som aktiverar snabb sökning. Table service skapar dock inga sekundära index så att dessa är de enda två indexerade egenskaperna (några av mönstren som beskrivs senare visar hur du kan kringgå den här synliga begränsningen).  

En tabell består av en eller flera partitioner. du kommer att se många av de design beslut som du gör genom att välja en lämplig **PartitionKey** och **RowKey** för att optimera din lösning. En lösning kan bestå av bara en enda tabell som innehåller alla entiteter som är ordnade i partitioner, men vanligt vis har en lösning flera tabeller. Tabeller hjälper dig att logiskt organisera dina entiteter, hjälper dig att hantera åtkomst till data med hjälp av åtkomst kontrol listor och du kan släppa en hel tabell med en enda lagrings åtgärd.  

### <a name="table-partitions"></a>Tabellpartitioner
Konto namnet, tabell namnet och **PartitionKey** tillsammans identifierar partitionen i lagrings tjänsten där tabell tjänsten lagrar entiteten. Som en del av adresserings planen för entiteter definierar partitionerna en omfattning för transaktioner (se [enhets grupp transaktioner](#entity-group-transactions) nedan) och utgör grunden för hur tabell tjänsten skalar. Mer information om partitioner finns i [Azure Storage skalbarhets-och prestanda mål](../storage/common/storage-scalability-targets.md).  

I Table service kan en enskild nod betjäna en eller flera kompletta partitioner och tjänsten skalas genom en dynamisk belastnings utjämning partition över noderna. Om en nod är under inläsning kan tabell tjänsten *dela* det intervall med partitioner som hanteras av noden på olika noder. När trafik under sidor, kan tjänsten *sammanfoga* partitionernas intervall från tysta noder tillbaka till en enda nod.  

Mer information om den interna informationen om Table service och i synnerhet hur tjänsten hanterar partitioner finns i pappers [Microsoft Azure Storage: en moln lagrings tjänst med hög tillgänglighet med stark konsekvens](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Enhets grupp transaktioner
I Table service är enhets grupp transaktioner (EGTs) den enda inbyggda mekanismen för att utföra atomiska uppdateringar över flera entiteter. EGTs kallas även *batch-transaktioner* i viss dokumentation. EGTs kan bara användas på entiteter som är lagrade i samma partition (samma partitionsnyckel i en specifik tabell), så när som helst behöver du Atomic-transaktionell beteende över flera entiteter som du behöver för att säkerställa att dessa entiteter finns i samma partition. Detta är ofta en orsak till att hålla flera entitetstyper i samma tabell (och partition) och inte använda flera tabeller för olika typer av enheter. En enda avställning kan köras på högst 100 entiteter.  Om du skickar flera samtidiga EGTs för bearbetning, är det viktigt att se till att dessa EGTs inte fungerar på entiteter som är gemensamma för EGTs, eftersom bearbetning av annan anledning kan fördröjas.

EGTs introducerar också en potentiell kompromiss som du kan använda för att utvärdera i din design: om du använder fler partitioner ökar skalbarheten för ditt program eftersom Azure har fler möjligheter till belastnings Utjämnings begär anden över noder, men det kan begränsa möjligheten för ditt program för att utföra atomiska transaktioner och upprätthålla stark konsekvens för dina data. Dessutom finns det vissa skalbara mål på nivån för en partition som kan begränsa data flödet för transaktioner som du kan förväntar dig för en enda nod: Mer information om skalbarhets målen för Azure Storage-konton och tabell tjänsten finns i [Azure Storage skalbarhets-och prestanda mål](../storage/common/storage-scalability-targets.md). I senare avsnitt i den här guiden beskrivs olika design strategier som hjälper dig att hantera kompromisser, till exempel den här, och diskuterar hur du bäst väljer din partitionsnyckel baserat på klient programmets specifika krav.  

### <a name="capacity-considerations"></a>Kapacitets överväganden
Följande tabell innehåller några av de viktigaste värdena som du bör känna till när du skapar en Table service lösning:  

| Total kapacitet för ett Azure Storage-konto | 500 TB |
| --- | --- |
| Antal tabeller i ett Azure Storage-konto |Begränsas bara av lagrings kontots kapacitet |
| Antal partitioner i en tabell |Begränsas bara av lagrings kontots kapacitet |
| Antal entiteter i en partition |Begränsas bara av lagrings kontots kapacitet |
| Storlek på en enskild entitet |Upp till 1 MB med högst 255 egenskaper (inklusive **PartitionKey**, **RowKey**och **tidsstämpel**) |
| Storlek på **PartitionKey** |En sträng som är upp till 1 KB stor |
| Storlek på **RowKey** |En sträng som är upp till 1 KB stor |
| Storlek på en enhets grupps transaktion |En transaktion kan innehålla högst 100 entiteter och nytto lasten måste vara mindre än 4 MB. En EGT kan bara uppdatera en entitet en gång. |

Mer information finns i [Understanding the Table Service Data Model](https://msdn.microsoft.com/library/azure/dd179338.aspx) (Så här fungerar datamodellen för Table Storage).  

### <a name="cost-considerations"></a>Kostnadsöverväganden
Table Storage är relativt billigt, men du bör inkludera kostnads uppskattningar för både kapacitets användning och antalet transaktioner som en del av din utvärdering av en lösning som använder Table service. I många fall är det dock ett giltigt tillvägagångs sätt att lagra denormaliserade eller duplicerade data för att förbättra din lösnings prestanda eller skalbarhet. Mer information om priser finns i [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Riktlinjer för tabelldesign
Listorna sammanfattar några av de viktigaste rikt linjerna som du bör tänka på när du utformar dina tabeller, och den här guiden kommer att hantera alla detaljer längre fram i. Dessa rikt linjer skiljer sig från de rikt linjer som du vanligt vis följer för relationens databas design.  

Att utforma din Table services lösning så att den blir *mer* effektiv:

* ***Design för frågor i Läs-tung-program.*** När du utformar dina tabeller bör du tänka på frågorna (särskilt svars tiden) som du ska köra innan du funderar på hur du kommer att uppdatera dina entiteter. Detta leder vanligt vis till en effektiv och genomförd lösning.  
* ***Ange både PartitionKey och RowKey i dina frågor.*** *Punkt frågor* som dessa är de mest effektiva tabell tjänst frågorna.  
* ***Överväg att lagra kopior av entiteter.*** Table Storage är billigt så att du kan lagra samma entitet flera gånger (med olika nycklar) för att möjliggöra mer effektiva frågor.  
* ***Överväg att avnormalisera dina data.*** Table Storage är billigt så att du kan avnormalisera dina data. Lagra till exempel sammanfattnings enheter så att frågor för sammanställd data bara behöver ha åtkomst till en enda entitet.  
* ***Använd sammansatta nyckel värden.*** De enda nycklar du har är **PartitionKey** och **RowKey**. Använd t. ex. värden för sammansatta nycklar för att aktivera alternativa, åtkomst vägar till entiteter.  
* ***Använd frågans projektion.*** Du kan minska mängden data som överförs via nätverket med hjälp av frågor som bara väljer de fält som du behöver.  

Designa din Table service-lösning så att den blir *Skriv* effektiv:  

* ***Skapa inte varma partitioner.*** Välj nycklar som gör att du kan sprida dina begär anden över flera partitioner vid en viss tidpunkt.  
* ***Undvik toppar i trafik.*** Utjämna trafiken under en rimlig tids period och Undvik toppar i trafiken.
* ***Skapa inte nödvändigt vis en separat tabell för varje typ av entitet.*** När du kräver atomiska transaktioner över entitetstyper kan du lagra dessa typer av flera entiteter i samma partition i samma tabell.
* ***Ta hänsyn till det maximala data flöde som du måste uppnå.*** Du måste vara medveten om skalbarhets målen för Table service och se till att designen inte kommer att överskrida dem.  

När du läser den här guiden visas exempel som har alla dessa principer i praktiken.  

## <a name="design-for-querying"></a>Utforma för frågor
Table service lösningar kan vara av intensiv, skriv intensiv eller en blandning av de två. Det här avsnittet fokuserar på saker som du bör tänka på när du utformar Table service som stöder Läs åtgärder effektivt. Vanligt vis är en design som stöder Läs åtgärder effektiv för Skriv åtgärder. Det finns dock ytterligare saker att tänka på när du utformar för att stödja Skriv åtgärder, som beskrivs i nästa avsnitt, [design för data ändringar](#design-for-data-modification).

En bra utgångs punkt för att utforma din Table service-lösning så att du kan läsa data effektivt är att fråga "vilka frågor som mitt program måste köra för att hämta de data som krävs från Table service?"  

> [!NOTE]
> Med Table service är det viktigt att du får fram rätt design eftersom det är svårt och dyrt att ändra det senare. I en Relations databas är det ofta möjligt att åtgärda prestanda problem genom att lägga till index i en befintlig databas: Detta är inte ett alternativ med Table service.  
> 
> 

Det här avsnittet fokuserar på de viktiga problem som du måste åtgärda när du utformar dina tabeller för frågor. De avsnitt som beskrivs i det här avsnittet är:

* [Hur du väljer PartitionKey och RowKey påverkar frågans prestanda](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Välja en lämplig PartitionKey](#choosing-an-appropriate-partitionkey)
* [Optimera frågor för Table service](#optimizing-queries-for-the-table-service)
* [Sortera data i Table service](#sorting-data-in-the-table-service)

### <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Hur du väljer PartitionKey och RowKey påverkar frågans prestanda
I följande exempel förutsätts att tabell tjänsten lagrar anställdas entiteter med följande struktur (de flesta exempel utesluter egenskapen **timestamp** för klarhet):  

| *Kolumn namn* | *Datatyp* |
| --- | --- |
| **PartitionKey** (avdelnings namn) |Sträng |
| **RowKey** (medarbetar-ID) |Sträng |
| **FirstName** |Sträng |
| **LastName** |Sträng |
| **Tids** |Integer |
| **EmailAddress** |Sträng |

I den tidigare delen av Azure Table service-översikten beskrivs några av de viktigaste funktionerna i Azure-Table service som direkt påverkar utformningen av frågor. Detta resulterar i följande allmänna rikt linjer för att utforma Table service frågor. Den filter-syntax som används i exemplen nedan är från Table service REST API, mer information finns i [fråga entiteter](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* En ***punkt fråga*** är den mest effektiva sökningen som ska användas och rekommenderas för att använda stora volymer eller sökningar som kräver lägsta latens. En sådan fråga kan använda indexen för att hitta en enskild entitet effektivt genom att ange både **PartitionKey** -och **RowKey** -värden. Exempel: $filter = (PartitionKey EQ ' Sales ') och (RowKey EQ 2)  
* Det andra bästa är en ***intervall fråga*** som använder **PartitionKey** och filter på ett intervall med **RowKey** -värden för att returnera mer än en entitet. **PartitionKey** -värdet identifierar en viss partition och **RowKey** -värdena identifierar en delmängd av entiteterna i den partitionen. Exempel: $filter = PartitionKey EQ ' Sales ' och RowKey ge ' och RowKey lt '  
* Det tredje bästa är en ***partitions ökning*** som använder **PartitionKey** och filter på en annan icke-nyckel egenskap och som kan returnera fler än en entitet. **PartitionKey** -värdet identifierar en viss partition och egenskaps värden väljer för en delmängd av entiteterna i den partitionen. Exempel: $filter = PartitionKey EQ ' Sales ' och LastName ' Svensson '  
* En ***tabells ökning*** inkluderar inte **PartitionKey** och är ineffektiv eftersom den söker igenom alla partitioner som utgör tabellen i tur och följd för matchande entiteter. En tabells ökning utförs oavsett om filtret använder **RowKey**eller inte. Exempel: $filter = LastName EQ ' Johansson '  
* Azure Table Storage frågor som returnerar flera entiteter returnerar dem sorterade i **PartitionKey** och **RowKey** -ordning. Välj en **RowKey** som definierar den vanligaste sorterings ordningen för att undvika att enheterna i klienten används. Frågeresultat som returneras av Azure-Tabell-API i Azure Cosmos DB sorteras inte efter partitionsnyckel eller rad nyckel. En detaljerad lista över funktions skillnader finns i [skillnader mellan tabell-API i Azure Cosmos DB och Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Om du använder en "**eller**" för att ange ett filter baserat på **RowKey** -värden uppstår en partitions ökning och behandlas inte som en områdes fråga. Därför bör du undvika frågor som använder filter som: $filter = PartitionKey EQ ' Sales ' och (RowKey EQ ' 121 ' eller RowKey EQ ' 322 ')  

Exempel på kod på klient sidan som använder lagrings klient biblioteket för att köra effektiva frågor finns i:  

* [Köra en punkt fråga med lagrings klient biblioteket](#executing-a-point-query-using-the-storage-client-library)
* [Hämtar flera entiteter med LINQ](#retrieving-multiple-entities-using-linq)
* [Projektion på Server Sidan](#server-side-projection)  

Exempel på kod på klient sidan som kan hantera flera enhets typer som lagras i samma tabell finns i:  

* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  

### <a name="choosing-an-appropriate-partitionkey"></a>Välja en lämplig PartitionKey
Valet av **PartitionKey** bör balansera behovet av att möjliggöra användningen av EGTs (för att säkerställa konsekvens) mot kravet att distribuera dina entiteter över flera partitioner (för att säkerställa en skalbar lösning).  

Med en extrem enhet kan du lagra alla entiteter i en enda partition, men detta kan begränsa din lösnings skalbarhet och förhindra att tabell tjänsten kan läsa in-balans-begäranden. På de andra Extreme kan du lagra en entitet per partition, som skulle vara mycket skalbar och som gör det möjligt för tabell tjänsten att belastningsutjämna begär Anden, men som hindrar dig från att använda enhets grupp transaktioner.  

En idealisk **PartitionKey** är en som gör att du kan använda effektiva frågor och som har tillräckligt med partitioner för att säkerställa att din lösning är skalbar. Normalt kommer du att se att dina entiteter har en lämplig egenskap som distribuerar dina entiteter över tillräckligt många partitioner.

> [!NOTE]
> I ett system som lagrar information om användare eller anställda kan UserID till exempel vara en felfri PartitionKey. Du kan ha flera entiteter som använder ett angivet UserID som partitionsnyckel. Varje entitet som lagrar data om en användare grupperas i en enda partition, så dessa entiteter är tillgängliga via enhets grupp transaktioner, samtidigt som de fortfarande är mycket skalbara.
> 
> 

Det finns ytterligare saker du behöver tänka på när du väljer **PartitionKey** som rör hur du ska infoga, uppdatera och ta bort entiteter: se avsnittet [design för data ändring](#design-for-data-modification) nedan.  

### <a name="optimizing-queries-for-the-table-service"></a>Optimera frågor för Table service
Table service indexerar automatiskt dina entiteter med hjälp av värdena **PartitionKey** och **RowKey** i ett enda grupperat index, och därför är orsaken till att frågor är den mest effektiva att använda. Det finns dock inga andra index än det i det grupperade indexet på **PartitionKey** och **RowKey**.

Många design komponenter måste uppfylla kraven för att kunna söka efter entiteter baserat på flera kriterier. Du kan till exempel hitta anställdas entiteter baserat på e-post, medarbetar-ID eller efter namn. Följande mönster i [design mönster](#table-design-patterns) för avsnitts tabeller behandlar de här typerna av krav och beskriver olika sätt att undvika det faktum att Table service inte tillhandahåller sekundära index:  

* [Sekundärt index mönster för sekundär partition](#intra-partition-secondary-index-pattern) – lagra flera kopior av varje entitet med olika **RowKey** -värden (i samma partition) för att möjliggöra snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika **RowKey** -värden.  
* [Sekundärt index mönster mellan partitioner](#inter-partition-secondary-index-pattern) – lagra flera kopior av varje entitet med olika **RowKey** -värden i separata partitioner eller i separata tabeller för att möjliggöra snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika  **RowKey** -värden.  
* [Mönster för index entiteter](#index-entities-pattern) – underhålla index enheter för att möjliggöra effektiva sökningar som returnerar listor med entiteter.  

### <a name="sorting-data-in-the-table-service"></a>Sortera data i Table service

Frågeresultat som returneras av Table service sorteras i stigande ordning baserat på **PartitionKey** och sedan efter **RowKey**.

> [!NOTE]
> Frågeresultat som returneras av Azure-Tabell-API i Azure Cosmos DB sorteras inte efter partitionsnyckel eller rad nyckel. En detaljerad lista över funktions skillnader finns i [skillnader mellan tabell-API i Azure Cosmos DB och Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

Nycklar i Azure Storage tabell är sträng värden och för att se till att numeriska värden sorteras korrekt bör du konvertera dem till en fast längd och fylla dem med nollor. Om till exempel det medarbetar-ID-värde som du använder som **RowKey** är ett heltals värde, bör du konvertera anställnings-ID **123** till **00000123**. 

Många program har krav för att använda data sorterade i olika ordningar: till exempel att sortera anställda efter namn eller genom att ansluta till datum. Följande mönster i avsnittets [design mönster](#table-design-patterns) för avsnitts design gör hur du kan använda alternativa sorterings ordningar för entiteterna:  

* [Sekundärt index mönster för sekundär partition](#intra-partition-secondary-index-pattern) – lagra flera kopior av varje entitet med olika RowKey-värden (i samma partition) för att möjliggöra snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika RowKey-värden.  
* [Sekundärt index mönster mellan partitioner](#inter-partition-secondary-index-pattern) – lagra flera kopior av varje entitet med olika RowKey-värden i separata partitioner i separata tabeller för att möjliggöra snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika RowKey-värden.
* [Logg änden-mönster](#log-tail-pattern) – hämta de *n* entiteter som senast har lagts till i en partition med hjälp av ett **RowKey** -värde som sorterar efter omvänt datum-och tids ordning.  

## <a name="design-for-data-modification"></a>Utforma för dataändring
Det här avsnittet fokuserar på design överväganden för att optimera infogningar, uppdateringar och borttagningar. I vissa fall måste du utvärdera de olika design modeller som optimeras för frågor mot designer som optimerar för data ändringar precis som du gör i design för Relations databaser (även om teknikerna för att hantera design handeln är annorlunda i en Relations databas). [Design mönster](#table-design-patterns) för avsnitts tabell beskriver några detaljerade design mönster för Table service och markerar några av dessa kompromisser. I praktiken kommer du att se att många design som är optimerade för att fråga entiteter också fungerar bra för att ändra entiteter.  

### <a name="optimizing-the-performance-of-insert-update-and-delete-operations"></a>Optimera prestanda för INSERT-, Update-och Delete-åtgärder
Om du vill uppdatera eller ta bort en entitet måste du kunna identifiera den med hjälp av värdena **PartitionKey** och **RowKey** . I detta hänseende bör ditt val av **PartitionKey** och **RowKey** för att ändra entiteter följa liknande kriterier som du väljer för att kunna använda punkt frågor eftersom du vill identifiera entiteter så effektivt som möjligt. Du vill inte använda en ineffektiv partition eller tabells ökning för att hitta en entitet för att identifiera de **PartitionKey** -och **RowKey** -värden som du behöver uppdatera eller ta bort.  

Följande mönster i avsnittets [design mönster](#table-design-patterns) adress optimerar prestandan eller åtgärderna för att infoga, uppdatera och ta bort:  

* [Mönster för hög volym borttagning](#high-volume-delete-pattern) – aktivera borttagning av en stor mängd entiteter genom att lagra alla entiteter för samtidig borttagning i en egen separat tabell. du tar bort entiteterna genom att ta bort tabellen.  
* [Data serie mönster](#data-series-pattern) – lagra kompletta data serier i en enda entitet för att minimera antalet begär Anden som du gör.  
* [Mönster för breda entiteter](#wide-entities-pattern) – Använd flera fysiska entiteter för att lagra logiska entiteter med fler än 252 egenskaper.  
* [Mönster för stora entiteter](#large-entities-pattern) – Använd Blob Storage för att lagra stora egenskaps värden.  

### <a name="ensuring-consistency-in-your-stored-entities"></a>Säkerställa konsekvens i dina lagrade entiteter
Den andra viktiga faktorn som påverkar ditt val av nycklar för optimering av data ändringar är hur man säkerställer konsekvens genom att använda atomiska transaktioner. Du kan bara använda en avhjälpning för att hantera entiteter som lagras i samma partition.  

Följande mönster i avsnittets [design mönster](#table-design-patterns) adress hanterar konsekvens:  

* [Sekundärt index mönster för sekundär partition](#intra-partition-secondary-index-pattern) – lagra flera kopior av varje entitet med olika **RowKey** -värden (i samma partition) för att möjliggöra snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika **RowKey** -värden.  
* [Sekundärt index mönster mellan partitioner](#inter-partition-secondary-index-pattern) – lagra flera kopior av varje entitet med olika RowKey-värden i separata partitioner eller i separata tabeller för att möjliggöra snabba och effektiva sökningar och alternativa sorterings ordningar med hjälp av olika **RowKey** parametervärden.  
* Till [sist konsekventa transaktions mönster](#eventually-consistent-transactions-pattern) – aktivera konsekvent beteende för partitionerings gränser eller lagrings system gränser med hjälp av Azure-köer.
* [Mönster för index entiteter](#index-entities-pattern) – underhålla index enheter för att möjliggöra effektiva sökningar som returnerar listor med entiteter.  
* [Utnormaliserings mönster](#denormalization-pattern) – kombinera relaterade data tillsammans i en enda entitet så att du kan hämta alla data du behöver med en enda punkt fråga.  
* [Data serie mönster](#data-series-pattern) – lagra kompletta data serier i en enda entitet för att minimera antalet begär Anden som du gör.  

Information om enhets grupps transaktioner finns i avsnittet [enhets grupp transaktioner](#entity-group-transactions).  

### <a name="ensuring-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Att se till att designen för effektiva ändringar underlättar effektiva frågor
I många fall kan en design för effektiv fråga resultera i effektiva ändringar, men du bör alltid utvärdera om detta är fallet för ditt speciella scenario. Några av mönstren i [design mönster](#table-design-patterns) för avsnitts tabeller utvärderar explicit kompromisser mellan frågor och ändring av entiteter, och du bör alltid ta hänsyn till antalet av varje typ av åtgärd.  

Följande mönster i [design mönster](#table-design-patterns) för avsnitts tabeller riktar in dig mellan att utforma för effektiva frågor och design för effektiv data ändring:  

* [Sammansatt nyckel mönster](#compound-key-pattern) – Använd sammansatta **RowKey** -värden för att göra det möjligt för en klient att söka efter relaterade data med en enda punkt fråga.  
* [Logg änden-mönster](#log-tail-pattern) – hämta de *n* entiteter som senast har lagts till i en partition med hjälp av ett **RowKey** -värde som sorterar efter omvänt datum-och tids ordning.  

## <a name="encrypting-table-data"></a>Kryptera tabell data
Klient biblioteket för .NET-Azure Storage stöder kryptering av sträng egenskaper för enhets egenskaper för INSERT-och replace-åtgärder. De krypterade strängarna lagras i tjänsten som binära egenskaper, och de konverteras tillbaka till strängar efter dekrypteringen.    

För tabeller, förutom krypterings principen, måste användarna ange vilka egenskaper som ska krypteras. Detta kan göras genom att ange ett [EncryptProperty]-attribut (för POCO-entiteter som härleds från TableEntity) eller en krypterings lösare i alternativ för begäran. En krypterings lösare är ett ombud som tar en partitionsnyckel, rad nyckel och egenskaps namn och returnerar ett booleskt värde som anger om den egenskapen ska krypteras. Under krypteringen använder klient biblioteket den här informationen för att avgöra om en egenskap ska krypteras vid skrivning till kabeln. Delegaten ger också möjlighet att logiskt kringgå hur egenskaper krypteras. (Till exempel om X, krypterar sedan egenskap A, annars krypterar egenskaper A och B.) Du behöver inte ange den här informationen när du läser eller frågar entiteter.

Sammanslagning stöds inte för närvarande. Eftersom en delmängd av egenskaperna kan ha krypterats tidigare med en annan nyckel, så resulterar det i data förlust genom att bara sammanfoga de nya egenskaperna och uppdatera metadata. Sammanslagning av kräver att du gör extra tjänst anrop för att läsa den befintliga entiteten från tjänsten, eller genom att använda en ny nyckel per egenskap, som båda inte passar av prestanda skäl.     

Information om hur du krypterar tabell data finns i [kryptering på klient sidan och Azure Key Vault för Microsoft Azure Storage](../storage/common/storage-client-side-encryption.md).  

## <a name="modeling-relationships"></a>Modellera relationer
Att skapa domän modeller är ett viktigt steg i utformningen av komplexa system. Normalt använder du modellerings processen för att identifiera entiteter och relationerna mellan dem som ett sätt att förstå företags domänen och informera utformningen av ditt system. Det här avsnittet fokuserar på hur du kan översätta några av de vanliga Relations typerna i domän modeller till design för Table service. Processen för att mappa från en logisk data modell till en fysisk NoSQL-baserad data modell skiljer sig från den som användes när du skapar en Relations databas. Design av Relations databaser förutsätter vanligt vis en data normaliserings process som är optimerad för att minimera redundans – och en deklarativ fråge funktion som sammanfattar hur databasen fungerar.  

### <a name="one-to-many-relationships"></a>En-till-många-relationer
En-till-många-relationer mellan företags domän objekt inträffar ofta: till exempel är en avdelning många anställda. Det finns flera sätt att implementera en-till-många-relationer i Table service var och en med-och nack delar som kan vara relevanta för det specifika scenariot.  

Överväg exemplet på ett stort företag med flera nationella organisationer med tusentals avdelningar och personal enheter där varje avdelning har många anställda och varje medarbetare som är kopplad till en speciell avdelning. En metod är att lagra separata avdelnings-och anställdas enheter, till exempel följande:  

![Avdelnings-och medarbetar enhet][1]

Det här exemplet visar en implicit en-till-många-relation mellan typerna baserat på **PartitionKey** -värdet. Varje avdelning kan ha många anställda.  

I det här exemplet visas även en avdelnings enhet och dess relaterade anställdas entiteter i samma partition. Du kan välja att använda olika partitioner, tabeller eller till och med lagrings konton för de olika enhets typerna.  

En annan metod är att avnormalisera dina data och lagra endast anställdas enheter med avnormaliserade avdelnings data som visas i följande exempel. I det här scenariot kanske det här avnormaliserade tillvägagångs sättet inte är det bästa om du har ett krav för att kunna ändra informationen om en avdelnings hanterare, eftersom du måste uppdatera varje anställd på avdelningen för att göra detta.  

![Anställd entitet][2]

Mer information finns i avsnittet om [avnormaliserings mönster](#denormalization-pattern) senare i den här hand boken.  

I följande tabell sammanfattas de olika metoderna och nack delar med var och en av de metoder som beskrivs ovan för att lagra personal-och avdelnings enheter som har en en-till-många-relation. Du bör också fundera över hur ofta du förväntar dig att utföra olika åtgärder: det kan vara acceptabelt att ha en design som innehåller en dyr åtgärd om åtgärden bara sker sällan.  

<table>
<tr>
<th>Form</th>
<th>Proffs</th>
<th>Nackdelar</th>
</tr>
<tr>
<td>Separata entitetstyper, samma partition, samma tabell</td>
<td>
<ul>
<li>Du kan uppdatera en avdelnings enhet med en enda åtgärd.</li>
<li>Du kan använda en EGT för att upprätthålla konsekvens om du har ett krav på att ändra en avdelnings enhet när du uppdaterar/infogar/tar bort en anställds entitet. Om du till exempel underhåller ett antal anställda för varje avdelning.</li>
</ul>
</td>
<td>
<ul>
<li>Du kan behöva hämta både en anställd och en avdelnings enhet för vissa klient aktiviteter.</li>
<li>Lagrings åtgärder sker i samma partition. På hög transaktions volymer kan detta resultera i ett hotspot-område.</li>
<li>Du kan inte flytta en medarbetare till en ny avdelning med hjälp av en EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Separata entitetstyper, olika partitioner eller tabeller eller lagrings konton</td>
<td>
<ul>
<li>Du kan uppdatera en avdelnings enhet eller en anställds entitet med en enda åtgärd.</li>
<li>På hög transaktions volymer kan detta hjälpa till att sprida belastningen över flera partitioner.</li>
</ul>
</td>
<td>
<ul>
<li>Du kan behöva hämta både en anställd och en avdelnings enhet för vissa klient aktiviteter.</li>
<li>Du kan inte använda EGTs för att upprätthålla konsekvens när du uppdaterar/infogar/tar bort en medarbetare och uppdaterar en avdelning. Du kan till exempel uppdatera ett antal anställda i en avdelnings enhet.</li>
<li>Du kan inte flytta en medarbetare till en ny avdelning med hjälp av en EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Avnormalisera till en enhets typ</td>
<td>
<ul>
<li>Du kan hämta all information som du behöver med en enskild begäran.</li>
</ul>
</td>
<td>
<ul>
<li>Det kan vara dyrt att upprätthålla konsekvens om du behöver uppdatera avdelnings information (Detta kräver att du uppdaterar alla anställda på en avdelning).</li>
</ul>
</td>
</tr>
</table>

Hur du väljer mellan dessa alternativ och vilka av-och nack delar som är mest signifikanta beror på dina specifika program scenarier. Till exempel hur ofta ändrar du avdelnings enheter. behöver alla dina anställda-frågor ytterligare avdelnings information. hur nära är du på skalbarhets gränserna för dina partitioner eller ditt lagrings konto?  

### <a name="one-to-one-relationships"></a>En-till-en-relationer
Domän modeller kan innehålla en-till-en-relationer mellan entiteter. Om du behöver implementera en en-till-en-relation i Table service måste du också välja hur du länkar de två relaterade entiteterna när du behöver hämta dem. Den här länken kan vara antingen implicit, baserat på en konvention i nyckel värden eller explicit genom att en länk lagras i form av **PartitionKey** -och **RowKey** -värden i varje entitet till dess relaterade entitet. En beskrivning av huruvida du bör lagra relaterade entiteter i samma partition finns i avsnittet [en-till-många-relationer](#one-to-many-relationships).  

Det finns också implementerings överväganden som kan leda till att du implementerar en-till-en-relationer i Table service:  

* Hantering av stora entiteter (mer information finns i [mönster för stora entiteter](#large-entities-pattern)).  
* Implementera åtkomst kontroller (mer information finns i [kontrol lera åtkomst med signaturer för delad åtkomst](#controlling-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Anslut till klienten
Även om det finns olika sätt att modellera relationer i Table service bör du inte glömma att de två huvudsakliga orsakerna till att använda Table service är skalbarhet och prestanda. Om du hittar flera relationer som äventyrar prestanda och skalbarhet för din lösning, bör du fråga dig själv om det är nödvändigt att bygga alla data relationer i tabell designen. Du kanske kan förenkla designen och förbättra skalbarheten och prestandan för din lösning om du låter klient programmet utföra alla nödvändiga kopplingar.  

Om du till exempel har små tabeller som innehåller data som inte ändras ofta kan du hämta dessa data en gång och cachelagra den på klienten. Detta kan undvika upprepade turer för att hämta samma data. I exemplen som vi har tittat på i den här hand boken är uppsättningen avdelningar i en liten organisation troligt vis små och ändra sällan så att det blir en bra kandidat för data som klient programmet kan ladda ned en gång och cachelagra som uppslags data.  

### <a name="inheritance-relationships"></a>Arvs relationer
Om klient programmet använder en uppsättning klasser som utgör en del av en arvs relation som representerar affär senheter, kan du enkelt spara dessa entiteter i Table service. Du kan till exempel ha följande uppsättning klasser definierade i klient programmet där **personen** är en abstrakt klass.

![ER-diagram över arvs relationer][3]

Du kan spara instanser av de två konkreta klasserna i Table service med hjälp av en enda person tabell med hjälp av entiteter i som ser ut så här:  

![Diagram över entiteten kund och anställd][4]

Mer information om hur du arbetar med flera olika entitetstyper i samma tabell i klient koden finns i avsnittet [arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types) senare i den här hand boken. Detta ger exempel på hur du kan identifiera enhets typen i klient koden.  

## <a name="table-design-patterns"></a>Tabell design mönster
I föregående avsnitt har du sett några detaljerade diskussioner om hur du optimerar tabell designen för att hämta enhets data med hjälp av frågor och för att infoga, uppdatera och ta bort enhets data. I det här avsnittet beskrivs några mönster som lämpar sig för användning med Table service lösningar. Dessutom får du se hur du praktiskt taget kan åtgärda några av de problem och kompromisser som har åtgärd ATS tidigare i den här hand boken. Följande diagram sammanfattar relationerna mellan olika mönster:  

![Bild av tabell design mönster][5]

Mönster kartan ovan markerar några relationer mellan mönster (blå) och anti-mönster (orange) som dokumenteras i den här guiden. Det finns många andra mönster som är värda att tänka på. Ett av de viktigaste scenarierna för tabell tjänst är till exempel att använda det [materialiserade visnings mönstret](https://msdn.microsoft.com/library/azure/dn589782.aspx) från [CQRS-mönstret (Command Query ansvars fördelning)](https://msdn.microsoft.com/library/azure/jj554200.aspx) .  

### <a name="intra-partition-secondary-index-pattern"></a>Sekundärt index mönster för sekundär partition
Lagra flera kopior av varje entitet med olika **RowKey** -värden (i samma partition) för att möjliggöra snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika **RowKey** -värden. Uppdateringar mellan kopior kan förvaras konsekvent med EGTs.  

#### <a name="context-and-problem"></a>Kontext och problem
Table service indexerar automatiskt entiteter med hjälp av värdena **PartitionKey** och **RowKey** . Detta gör att ett klient program kan hämta en entitet effektivt med dessa värden. Om du till exempel använder tabell strukturen som visas nedan kan ett klient program använda en punkt fråga för att hämta en enskild anställd entitet med hjälp av avdelnings namnet och anställnings-ID: t ( **PartitionKey** och **RowKey** ). En-klient kan också hämta entiteter sorterade efter anställnings-ID inom varje avdelning.

![Anställd entitet][6]

Om du också vill kunna hitta en anställd entitet baserat på värdet för en annan egenskap, till exempel e-postadress, måste du använda en mindre effektiv partitions ökning för att hitta en matchning. Detta beror på att tabell tjänsten inte tillhandahåller sekundära index. Det finns dessutom inget alternativ för att begära en lista över anställda sorterad i en annan ordning än **RowKey** ordning.  

#### <a name="solution"></a>Lösning
För att undvika avsaknad av sekundära index kan du lagra flera kopior av varje entitet med varje kopia med ett annat **RowKey** -värde. Om du lagrar en entitet med de strukturer som visas nedan kan du effektivt hämta personal enheter baserat på e-postadress eller medarbetar-ID. Prefixvärde för **RowKey**, "empid_" och "email_" gör att du kan fråga efter en enskild anställd eller ett antal anställda genom att använda ett intervall med e-postadresser eller anställnings-ID.  

![Anställd entitet med varierande RowKey-värden][7]

Följande två filter villkor (en sökning efter anställnings-ID och en sökning efter e-postadress) anger båda punkt frågor:  

* $filter = (PartitionKey EQ Sales) och (RowKey EQ empid_000223)  
* $filter = (PartitionKey EQ Sales) och (RowKey EQemail_jonesj@contoso.com)  

Om du frågar efter ett antal anställdas entiteter kan du ange ett intervall som sorterats i anställnings-ID-ordning, eller ett intervall som sorterats i e-postadressen genom att fråga efter entiteter med lämpligt prefix i **RowKey**.  

* Om du vill hitta alla anställda på försäljnings avdelningen med ett anställnings-ID inom intervallet 000100 till 000199 använder du: $filter = (PartitionKey EQ Sales) och (RowKey ge "empid_000100") och (RowKey Le ' empid_000199 ')  
* Om du vill hitta alla anställda på försäljnings avdelningen med en e-postadress som börjar med bokstaven "a", använder du $filter = (PartitionKey EQ Sales) och (RowKey ge "email_a") och (RowKey lt ' email_b ')  
  
  Den filter-syntax som används i exemplen ovan är från Table service REST API, mer information finns i [fråga entiteter](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Table Storage är relativt billigt att använda så kostnaden för lagring av dubblettdata bör inte vara en stor angelägenhet. Du bör dock alltid utvärdera kostnaden för din design baserat på dina förväntade lagrings krav och bara lägga till dubbla entiteter som stöder de frågor som klient programmet ska köra.  
* Eftersom de sekundära index enheterna lagras i samma partition som de ursprungliga entiteterna, bör du se till att du inte överskrider skalbarhets målen för en enskild partition.  
* Du kan hålla dina dubbla entiteter konsekventa med varandra genom att använda EGTs för att uppdatera de två kopiorna av entiteten. Detta innebär att du bör lagra alla kopior av en entitet i samma partition. Mer information finns i avsnittet [använda enhets grupp transaktioner](#entity-group-transactions).  
* Värdet som används för **RowKey** måste vara unikt för varje entitet. Överväg att använda sammansatta nyckel värden.  
* Utfyllnad av numeriska värden i **RowKey** (till exempel anställnings-ID 000223), aktiverar korrekt sortering och filtrering baserat på övre och nedre gränser.  
* Du behöver inte duplicera alla egenskaper för entiteten. Om till exempel frågorna som söker efter entiteter som använder e-postadressen i **RowKey** aldrig behöver medarbetarens ålder, kan dessa entiteter ha följande struktur:

![Anställd entitet][8]

* Det är vanligt vis bättre att lagra dubblettdata och se till att du kan hämta alla data du behöver med en enda fråga, än att använda en fråga för att hitta en entitet och en annan för att leta upp nödvändiga data.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när ditt klient program behöver hämta entiteter med olika olika nycklar, när klienten behöver hämta entiteter i olika sorterings ordningar och där du kan identifiera varje entitet med hjälp av en mängd unika värden. Du bör dock vara säker på att du inte överskrider gränserna för skalbarhet när du utför enhets uppslag med de olika **RowKey** -värdena.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sekundärt index mönster mellan partitioner](#inter-partition-secondary-index-pattern)
* [Sammansatt nyckel mönster](#compound-key-pattern)
* [Enhets grupp transaktioner](#entity-group-transactions)
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Sekundärt index mönster mellan partitioner
Lagra flera kopior av varje entitet med olika **RowKey** -värden i separata partitioner eller i separata tabeller för att möjliggöra snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika **RowKey** -värden.  

#### <a name="context-and-problem"></a>Kontext och problem
Table service indexerar automatiskt entiteter med hjälp av värdena **PartitionKey** och **RowKey** . Detta gör att ett klient program kan hämta en entitet effektivt med dessa värden. Om du till exempel använder tabell strukturen som visas nedan kan ett klient program använda en punkt fråga för att hämta en enskild anställd entitet med hjälp av avdelnings namnet och anställnings-ID: t ( **PartitionKey** och **RowKey** ). En-klient kan också hämta entiteter sorterade efter anställnings-ID inom varje avdelning.  

![Anställd entitet][9]

Om du också vill kunna hitta en anställd entitet baserat på värdet för en annan egenskap, till exempel e-postadress, måste du använda en mindre effektiv partitions ökning för att hitta en matchning. Detta beror på att tabell tjänsten inte tillhandahåller sekundära index. Det finns dessutom inget alternativ för att begära en lista över anställda sorterad i en annan ordning än **RowKey** ordning.  

Du förväntar dig en stor mängd transaktioner mot dessa entiteter och vill minimera risken för Table service hastighet som begränsar din klient.  

#### <a name="solution"></a>Lösning
För att undvika avsaknad av sekundära index kan du lagra flera kopior av varje entitet med varje kopia med olika **PartitionKey** -och **RowKey** -värden. Om du lagrar en entitet med de strukturer som visas nedan kan du effektivt hämta personal enheter baserat på e-postadress eller medarbetar-ID. Prefixvärde för **PartitionKey**, "empid_" och "email_" gör att du kan identifiera vilket index som du vill använda för en fråga.  

![Anställd entitet med primärt index och anställd entitet med sekundärt index][10]

Följande två filter villkor (en sökning efter anställnings-ID och en sökning efter e-postadress) anger båda punkt frågor:  

* $filter = (PartitionKey EQ empid_Sales) och (RowKey EQ 000223)
* $filter = (PartitionKey EQ email_Sales) och (RowKey EQjonesj@contoso.com)  

Om du frågar efter ett antal anställdas entiteter kan du ange ett intervall som sorterats i anställnings-ID-ordning, eller ett intervall som sorterats i e-postadressen genom att fråga efter entiteter med lämpligt prefix i **RowKey**.  

* Om du vill hitta alla anställda på försäljnings avdelningen med ett anställnings-ID i intervallet **000100** till **000199** sorterat i anställnings-id order användning: $filter = (PartitionKey EQ Empid_Sales) och (RowKey ge "000100") och (RowKey Le ' 000199 ')  
* Om du vill hitta alla anställda på försäljnings avdelningen med en e-postadress som börjar med "a" sorterad i e-postadressen använder du: $filter = (PartitionKey EQ email_Sales) och (RowKey ge "a") och (RowKey lt ' b ')  

Observera att filter syntaxen som används i exemplen ovan kommer från Table service REST API, mer information finns i [fråga entiteter](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du kan se till att dina dubbla entiteter försätts i enlighet med varandra genom att använda mönstret för senare [transaktioner](#eventually-consistent-transactions-pattern) för att underhålla de primära och sekundära index enheterna.  
* Table Storage är relativt billigt att använda så kostnaden för lagring av dubblettdata bör inte vara en stor angelägenhet. Du bör dock alltid utvärdera kostnaden för din design baserat på dina förväntade lagrings krav och bara lägga till dubbla entiteter som stöder de frågor som klient programmet ska köra.  
* Värdet som används för **RowKey** måste vara unikt för varje entitet. Överväg att använda sammansatta nyckel värden.  
* Utfyllnad av numeriska värden i **RowKey** (till exempel anställnings-ID 000223), aktiverar korrekt sortering och filtrering baserat på övre och nedre gränser.  
* Du behöver inte duplicera alla egenskaper för entiteten. Om till exempel frågorna som söker efter entiteter som använder e-postadressen i **RowKey** aldrig behöver medarbetarens ålder, kan dessa entiteter ha följande struktur:
  
  ![Anställd entitet med sekundärt index][11]
* Det är vanligt vis bättre att lagra dubblettdata och se till att du kan hämta alla data du behöver med en enda fråga än att använda en fråga för att hitta en entitet med hjälp av det sekundära indexet och en annan för att söka efter nödvändiga data i det primära indexet.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när ditt klient program behöver hämta entiteter med olika olika nycklar, när klienten behöver hämta entiteter i olika sorterings ordningar och där du kan identifiera varje entitet med hjälp av en mängd unika värden. Använd det här mönstret om du vill undvika att överskrida gränserna för skalbarhet när du utför enhets uppslag med de olika **RowKey** -värdena.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Mönster för eventuellt konsekventa transaktioner](#eventually-consistent-transactions-pattern)  
* [Sekundärt index mönster för sekundär partition](#intra-partition-secondary-index-pattern)  
* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Enhets grupp transaktioner](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Mönster för eventuellt konsekventa transaktioner
Aktivera konsekvent beteende för partitionerings gränser eller lagrings system gränser med hjälp av Azure-köer.  

#### <a name="context-and-problem"></a>Kontext och problem
EGTs möjliggör atomiska transaktioner över flera entiteter som delar samma partitionsnyckel. Av prestanda-och skalbarhets skäl kan du välja att lagra entiteter som har konsekvens krav i separata partitioner eller i ett separat lagrings system: i ett sådant scenario kan du inte använda EGTs för att upprätthålla konsekvens. Du kan till exempel ha ett krav på att upprätthålla eventuell konsekvens mellan:  

* Entiteter som lagras i två olika partitioner i samma tabell, i olika tabeller eller i olika lagrings konton.  
* En entitet som lagras i Table service och en blob som lagras i Blob Service.  
* En entitet som lagras i Table service och en fil i ett fil system.  
* En enhets lagring i Table service som har indexerats med Azure Kognitiv sökning-tjänsten.  

#### <a name="solution"></a>Lösning
Genom att använda Azure-köer kan du implementera en lösning som ger en eventuell konsekvens på två eller flera partitioner eller lagrings system.
För att illustrera den här metoden förutsätter vi att du har ett krav för att kunna arkivera gamla personal enheter. Gamla personal enheter frågas sällan och bör undantas från aktiviteter som hanterar aktuella anställda. För att införa detta krav lagrar du aktiva medarbetare i den **aktuella** tabellen och gamla anställda i tabellen **Arkiv** . Genom att arkivera en medarbetare måste du ta bort entiteten från den **aktuella** tabellen och lägga till entiteten i **Arkiv** tabellen, men du kan inte använda en EGT för att utföra dessa två åtgärder. För att undvika risken att ett fel orsakar att en entitet visas i båda eller inga tabeller, måste Arkiv åtgärden vara konsekvent. I följande sekvensdiagram beskrivs stegen i den här åtgärden. Mer information finns för undantags Sök vägar i texten som följer.  

![Lösnings diagram för eventuell konsekvens][12]

En klient initierar Arkiv åtgärden genom att placera ett meddelande i en Azure-kö, i det här exemplet för att arkivera medarbetar #456. En arbets roll avsöker kön efter nya meddelanden. När den hittar ett läser det meddelandet och lämnar en dold kopia av kön. Arbets rollen hämtar sedan en kopia av entiteten från den **aktuella** tabellen, infogar en kopia i tabellen **Arkiv** och tar sedan bort originalet från den **aktuella** tabellen. Slutligen, om det inte fanns några fel från föregående steg, tar arbets rollen bort det dolda meddelandet från kön.  

I det här exemplet infogar steg 4 medarbetaren i **Arkiv** tabellen. Den kan lägga till medarbetaren till en BLOB i Blob Service eller en fil i ett fil system.  

#### <a name="recovering-from-failures"></a>Återställer från haverier
Det är viktigt att åtgärderna i steg **4** och **5** måste vara *idempotenta* om arbets rollen behöver starta om lagrings åtgärden. Om du använder Table service, för steg **4** , ska du använda en "Infoga eller Ersätt"-åtgärd. i steg **5** bör du använda åtgärden "ta bort om finns" i klient biblioteket som du använder. Om du använder ett annat lagrings system måste du använda en lämplig idempotenta-åtgärd.  

Om arbets rollen aldrig slutförs steg **6**, efter en tids gräns, visas meddelandet igen i kön redo för arbets rollen för att försöka att bearbeta det igen. Arbets rollen kan kontrol lera hur många gånger ett meddelande i kön har lästs och, om det behövs, flagga det är ett "Poison"-meddelande för undersökning genom att skicka det till en separat kö. Mer information om hur du läser Kömeddelanden och kontrollerar antalet ur kön finns i [Hämta meddelanden](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Vissa fel från tabell-och Queue-tjänsterna är tillfälliga fel och klient programmet bör inkludera lämplig omprövnings logik för att hantera dem.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Den här lösningen tillhandahåller inte transaktions isolering. En-klient kan till exempel läsa **aktuella** och **arkivera** tabeller när arbets rollen var mellan steg **4** och **5**och se en inkonsekvent vy över data. Data kommer att vara konsekventa.  
* Du måste vara säker på att steg 4 och 5 är idempotenta för att säkerställa eventuell konsekvens.  
* Du kan skala lösningen genom att använda flera köer och arbets Rolls instanser.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill garantera eventuell konsekvens mellan entiteter som finns i olika partitioner eller tabeller. Du kan utöka det här mönstret för att säkerställa eventuell konsekvens för åtgärder i Table service och Blob Service och andra icke-Azure Storage data källor, till exempel databas eller fil system.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Enhets grupp transaktioner](#entity-group-transactions)  
* [Sammanfoga eller Ersätt](#merge-or-replace)  

> [!NOTE]
> Om transaktions isolering är viktigt för din lösning bör du överväga att designa om tabellerna så att du kan använda EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Mönster för index entiteter
Underhåll index enheter för att aktivera effektiva sökningar som returnerar listor med entiteter.  

#### <a name="context-and-problem"></a>Kontext och problem
Table service indexerar automatiskt entiteter med hjälp av värdena **PartitionKey** och **RowKey** . Detta gör att ett klient program kan hämta en entitet effektivt med en punkt fråga. Med hjälp av tabell strukturen som visas nedan kan ett klient program effektivt hämta en enskild anställd entitet med hjälp av avdelnings namnet och medarbetar-ID: t ( **PartitionKey** och **RowKey**).  

![Anställd entitet][13]

Om du också vill kunna hämta en lista över anställdas entiteter baserat på värdet för en annan icke-unik egenskap, t. ex. efter namn, måste du använda en mindre effektiv partitions ökning för att hitta matchningar i stället för att använda ett index för att se dem direkt. Detta beror på att tabell tjänsten inte tillhandahåller sekundära index.  

#### <a name="solution"></a>Lösning
Om du vill aktivera sökning efter efter namn med enhets strukturen som visas ovan måste du underhålla listor med anställnings-ID. Om du vill hämta anställdas entiteter med ett visst efter namn, till exempel Johansson, måste du först leta upp listan med anställnings-ID för anställda med Johansson som efter namn och sedan hämta de anställdas entiteter. Det finns tre huvud alternativ för att lagra listor med anställnings-ID:  

* Använd Blob Storage.  
* Skapa index enheter i samma partition som de anställdas entiteter.  
* Skapa index enheter i en separat partition eller tabell.  

<u>Alternativ #1: Använd Blob Storage</u>  

För det första alternativet skapar du en BLOB för varje unikt efter namn och i varje BLOB-Arkiv visas en lista över värdena **PartitionKey** (avdelning) och **RowKey** (medarbetar-ID) för anställda som har det efter namnet. När du lägger till eller tar bort en medarbetare bör du se till att innehållet i den relevanta blobben stämmer överens med de anställdas enheter.  

<u>Alternativ #2:</u> Skapa index enheter i samma partition  

För det andra alternativet använder du indexera entiteter som lagrar följande data:  

![Anställd entitet med en sträng som innehåller en lista med anställnings-ID med samma efter namn][14]

Egenskapen **EmployeeIDs** innehåller en lista med anställnings-ID: n för anställda med efter namnet lagrat i **RowKey**.  

Följande steg beskriver processen som du bör följa när du lägger till en ny medarbetare om du använder det andra alternativet. I det här exemplet lägger vi till en anställd med ID 000152 och efter namn Jones på försäljnings avdelningen:  

1. Hämta index entiteten med **PartitionKey** -värdet "Sales" och **RowKey** -värdet "Johansson". Spara ETag för den här entiteten som ska användas i steg 2.  
2. Skapa en enhets grupp transaktion (det vill säga en batch-åtgärd) som infogar den nya personal enheten (**PartitionKey** värde "försäljning" och **RowKey** värde "000152") och uppdaterar index enheten (**PartitionKey** värde "Sales" och **RowKey** värdet "Johansson") genom att lägga till det nya medarbetar-ID: t i listan i fältet EmployeeIDs. Mer information om enhets grupps transaktioner finns i [enhets grupp transaktioner](#entity-group-transactions).  
3. Om enhets grupp transaktionen Miss lyckas på grund av ett optimistiskt samtidighets fel (någon annan har ändrat entiteten index) måste du börja om steg 1.  

Du kan använda en liknande metod för att ta bort en medarbetare om du använder det andra alternativet. Att ändra en anställds efter namn är något mer komplicerat eftersom du måste köra en enhets grupps transaktion som uppdaterar tre entiteter: den anställdas entitet, index-entiteten för det gamla efter namnet och entiteten index för det nya efter namnet. Du måste hämta varje entitet innan du gör några ändringar för att kunna hämta de ETag-värden som du sedan kan använda för att utföra uppdateringarna med optimistisk samtidighet.  

Följande steg beskriver processen som du bör följa när du behöver leta upp alla anställda med ett angivet efter namn på en avdelning om du använder det andra alternativet. I det här exemplet letar vi upp alla anställda med efter namn Jones på försäljnings avdelningen:  

1. Hämta index entiteten med **PartitionKey** -värdet "Sales" och **RowKey** -värdet "Johansson".  
2. Parsa listan med anställnings-ID: n i fältet EmployeeIDs.  
3. Om du behöver ytterligare information om var och en av dessa anställda (till exempel deras e-postadresser) kan du hämta var och en av de anställdas enheter med **PartitionKey** -värdet "Sales" och **RowKey** -värden från listan över medarbetare som du fick i steg 2.  

<u>Alternativ #3:</u> Skapa index enheter i en separat partition eller tabell  

För det tredje alternativet använder du indexera entiteter som lagrar följande data:  

![Anställd entitet med en sträng som innehåller en lista med anställnings-ID med samma efter namn][15]

Egenskapen **EmployeeIDs** innehåller en lista med anställnings-ID: n för anställda med efter namnet lagrat i **RowKey**.  

Med det tredje alternativet kan du inte använda EGTs för att upprätthålla konsekvens eftersom index entiteterna finns i en separat partition från entiteten anställda. Du bör se till att index entiteterna är konsekventa med de anställdas entiteter.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Den här lösningen kräver minst två frågor för att hämta matchande entiteter: en för att skicka en fråga till index entiteter för att hämta listan över **RowKey** -värden och sedan frågor för att hämta varje entitet i listan.  
* Med tanke på att en enskild entitet har en maximal storlek på 1 MB, alternativ #2 och alternativet #3 i lösningen förutsätter att listan med anställnings-ID: n för alla angivna efter namn aldrig är större än 1 MB. Om listan med anställnings-ID sannolikt är större än 1 MB använder du alternativet #1 och lagrar index data i Blob Storage.  
* Om du använder alternativet #2 (när du använder EGTs för att hantera att lägga till och ta bort medarbetare och ändra en anställds efter namn) måste du utvärdera om volymen av transaktionerna ska närma sig skalbarhets gränserna för en specifik partition. I så fall bör du överväga en konsekvent lösning (alternativ #1 eller alternativ #3) som använder köer för att hantera uppdaterings begär Anden och gör att du kan lagra dina index enheter i en separat partition från de anställdas enheter.  
* Om du #2 i den här lösningen förutsätter vi att du vill leta efter efter namn på en avdelning: till exempel vill du hämta en lista över anställda med efter namn Jones på försäljnings avdelningen. Om du vill kunna söka efter alla anställda med efter namn Jones i hela organisationen, använder du antingen alternativet #1 eller alternativ #3.
* Du kan implementera en Queue-baserad lösning som ger en eventuell konsekvens (mer information finns i mönster för senare [konsekventa transaktioner](#eventually-consistent-transactions-pattern) ).  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du vill söka efter en uppsättning entiteter som alla delar ett gemensamt egenskaps värde, till exempel alla anställda med efter namn Jones.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Mönster för eventuellt konsekventa transaktioner](#eventually-consistent-transactions-pattern)  
* [Enhets grupp transaktioner](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Utnormaliserings mönster
Kombinera relaterade data tillsammans i en enda entitet så att du kan hämta alla data du behöver med en enda punkt fråga.  

#### <a name="context-and-problem"></a>Kontext och problem
I en Relations databas normaliserar du vanligt vis data för att ta bort dubbletter som resulterar i frågor som hämtar data från flera tabeller. Om du normaliserar dina data i Azure-tabeller måste du göra flera tur och ingångar från klienten till servern för att hämta relaterade data. Med tabell strukturen som visas nedan behöver du till exempel två tur och svar för att hämta information om en avdelning: en för att hämta information om en avdelning: en för att hämta den avdelnings enhet som innehåller chefens ID och en annan begäran att hämta chefens information i en anställds entitet.  

![Avdelnings enhet och entitet för anställd][16]

#### <a name="solution"></a>Lösning
I stället för att lagra data i två separata entiteter avnormaliserar du data och behåller en kopia av chefens information i avdelnings enheten. Till exempel:  

![Avnormaliserad och kombinerad avdelnings enhet][17]

Med avdelnings enheter lagrade med dessa egenskaper kan du nu hämta all information du behöver om en avdelning med en punkt fråga.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Det finns vissa kostnads kostnader som är kopplade till att lagra vissa data två gånger. Prestanda förmånen (till följd av färre begär anden till lagrings tjänsten) väger vanligt vis att öka marginal ökningen i lagrings kostnaderna (och den här kostnaden delvis motbokas genom en minskning av antalet transaktioner som du behöver för att hämta information om en avdelning ).  
* Du måste upprätthålla konsekvensen för de två entiteter som lagrar information om chefer. Du kan hantera konsekvens problemet genom att använda EGTs för att uppdatera flera entiteter i en enda Atomic-transaktion: i det här fallet avdelnings enheten och den anställdas enhet för avdelnings chefen lagras på samma partition.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du ofta behöver söka efter relaterad information. Det här mönstret minskar antalet frågor som klienten måste göra för att hämta de data som krävs.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Enhets grupp transaktioner](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Sammansatt nyckel mönster
Använd sammansatta **RowKey** -värden för att göra det möjligt för en klient att söka efter relaterade data med en enda punkt fråga.  

#### <a name="context-and-problem"></a>Kontext och problem
I en Relations databas är det naturligt att använda kopplingar i frågor för att returnera relaterade data delar till klienten i en enskild fråga. Du kan till exempel använda medarbetar-ID: t för att leta upp en lista över relaterade entiteter som innehåller prestanda och granska data för den anställda.  

Anta att du lagrar anställdas entiteter i Table service med hjälp av följande struktur:  

![Anställd entitet][18]

Du måste också lagra historiska data som rör granskningar och prestanda för varje år som den anställde har arbetat för din organisation och du måste kunna komma åt den här informationen per år. Ett alternativ är att skapa en annan tabell som lagrar entiteter med följande struktur:  

![Personal gransknings enhet][19]

Observera att med den här metoden kan du välja att duplicera viss information (till exempel förnamn och efter namn) i den nya entiteten så att du kan hämta dina data med en enda begäran. Du kan dock inte upprätthålla stark konsekvens eftersom du inte kan använda en avkonsistens för att uppdatera de två entiteterna.  

#### <a name="solution"></a>Lösning
Lagra en ny entitetstyp i den ursprungliga tabellen med hjälp av entiteter med följande struktur:  

![Anställd entitet med sammansatt nyckel][20]

Observera att **RowKey** nu är en sammansatt nyckel som består av medarbetar-ID: t och året för gransknings data som gör det möjligt att hämta den anställdas prestanda och granska data med en enda begäran för en enda enhet.  

I följande exempel beskrivs hur du kan hämta alla gransknings data för en viss medarbetare (till exempel anställda 000123 på försäljnings avdelningen):  

$filter = (PartitionKey EQ ' Sales ') och (RowKey ge ' empid_000123 ') och (RowKey lt ' empid_000124 ') & $select = RowKey, chefs klassificering, peer-klassificering, kommentarer  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du bör använda ett lämpligt avgränsnings tecken som gör det enkelt att parsa **RowKey** -värdet: till exempel **000123_2012**.  
* Du lagrar även den här entiteten i samma partition som andra entiteter som innehåller relaterade data för samma anställd, vilket innebär att du kan använda EGTs för att upprätthålla stark konsekvens.
* Du bör fundera över hur ofta du kommer att fråga data för att avgöra om det här mönstret är lämpligt.  Om du till exempel kommer att få åtkomst till gransknings data sällan och den viktigaste informationen i den anställda bör du ha dem som separata entiteter.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra en eller flera relaterade entiteter som du frågar ofta.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Enhets grupp transaktioner](#entity-group-transactions)  
* [Arbeta med heterogena entitetstyper](#working-with-heterogeneous-entity-types)  
* [Mönster för eventuellt konsekventa transaktioner](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Logg änden-mönster
Hämta de *n* entiteter som senast har lagts till i en partition med hjälp av ett **RowKey** -värde som sorterar i omvänt datum-och tids ordning.  

> [!NOTE]
> Frågeresultat som returneras av Azure-Tabell-API i Azure Cosmos DB sorteras inte efter partitionsnyckel eller rad nyckel. Detta mönster är därför lämpligt för Azure Table Storage och inte Azure Cosmos DB. En detaljerad lista över funktions skillnader finns i [skillnader mellan tabell-API i Azure Cosmos DB och Azure Table Storage](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

#### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt krav är att kunna hämta de senast skapade entiteterna, till exempel de tio senaste utgifts anspråk som skickats av en anställd. Tabell frågor stöder en **$Top** Query-åtgärd för att returnera de första *n* entiteterna från en mängd: det finns ingen motsvarande fråga för att returnera de sista n entiteterna i en mängd.  

#### <a name="solution"></a>Lösning
Lagra entiteterna med en **RowKey** som naturligt sorterar i omvänd datum-/tids ordning genom att använda så att den senaste posten alltid är den första i tabellen.  

För att till exempel kunna hämta de tio senaste utgifts anspråk som skickats av en medarbetare, kan du använda ett omvänt skal värde som härletts från aktuellt datum/tid. I följande C# kod exempel visas ett sätt att skapa ett lämpligt "inverterade Tick"-värde för en **RowKey** som sorterar från den senaste till det äldsta:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Du kan gå tillbaka till datum/tid-värdet med hjälp av följande kod:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Tabell frågan ser ut så här:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Du måste fylla i det omvända skalet med inledande nollor för att sträng värden ska sorteras som förväntat.  
* Du måste vara medveten om skalbarhets målen på nivån för en partition. Var noga med att inte skapa varma dekor partitioner.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver åtkomst till entiteter i omvänd datum-/tids ordning eller när du behöver åtkomst till de senast tillagda entiteterna.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Lägga/Lägg till anti-mönster](#prepend-append-anti-pattern)  
* [Entiteter hämtas](#retrieving-entities)  

### <a name="high-volume-delete-pattern"></a>Hög volym borttagnings mönster
Aktivera borttagning av en stor mängd entiteter genom att lagra alla entiteter för samtidig borttagning i en egen separat tabell. du tar bort entiteterna genom att ta bort tabellen.  

#### <a name="context-and-problem"></a>Kontext och problem
Många program tar bort gamla data som inte längre behöver vara tillgängliga för ett klient program eller som programmet har arkiverat till ett annat lagrings medium. Du kan vanligt vis identifiera sådana data med ett datum: exempelvis har du ett krav för att ta bort poster för alla inloggnings begär Anden som är mer än 60 dagar gamla.  

En möjlig design är att använda datum och tid för inloggningsbegäran i **RowKey**:  

![Entitet för inloggnings försök][21]

Med den här metoden undviker du hotspot-filer för partitioner eftersom programmet kan infoga och ta bort inloggnings enheter för varje användare i en separat partition. Den här metoden kan dock vara kostsam och tids krävande om du har ett stort antal entiteter eftersom du först måste utföra en tabells ökning för att kunna identifiera alla entiteter som ska tas bort och sedan ta bort varje gammal entitet. Du kan minska antalet fördröjningar till servern som krävs för att ta bort de gamla entiteterna genom att gruppera flera borttagnings begär anden i EGTs.  

#### <a name="solution"></a>Lösning
Använd en separat tabell för varje dag då inloggnings försök görs. Du kan använda entitetens design ovan för att undvika hotspots när du infogar entiteter, och om du tar bort gamla entiteter är det nu bara en fråga om att ta bort en tabell varje dag (en enda lagrings åtgärd) i stället för att söka efter och ta bort hundratals och tusentals enskilda loggar in entiteter varje dag.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Stöder din design andra sätt att använda data, till exempel att leta upp specifika entiteter, länka till andra data eller generera sammanställd information?  
* Undviks dina design frekventa punkter när du infogar nya entiteter?  
* Vänta en stund om du vill återanvända samma tabell namn när du har tagit bort det. Det är bättre att alltid använda unika tabell namn.  
* En viss hastighets begränsning visas när du först använder en ny tabell medan Table service lär sig åtkomst mönstren och distribuerar partitionerna över noderna. Du bör fundera över hur ofta du behöver skapa nya tabeller.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du har en stor mängd entiteter som du måste ta bort samtidigt.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Enhets grupp transaktioner](#entity-group-transactions)
* [Ändra entiteter](#modifying-entities)  

### <a name="data-series-pattern"></a>Mönster för data serier
Lagra kompletta data serier i en enda entitet för att minimera antalet förfrågningar som du gör.  

#### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt scenario är att ett program lagrar en serie data som vanligt vis behöver hämta alla samtidigt. Ditt program kan till exempel registrera hur många snabb meddelanden varje medarbetare skickar varje timme och sedan använda den här informationen för att rita upp hur många meddelanden varje användare skickas under de senaste 24 timmarna. En design kan vara att lagra 24 entiteter för varje anställd:  

![Message stats-enhet][22]

Med den här designen kan du enkelt hitta och uppdatera entiteten för uppdatering för varje anställd när programmet behöver uppdatera värdet för antal meddelanden. Men för att hämta informationen för att rita ett diagram över aktiviteten under de senaste 24 timmarna måste du hämta 24 entiteter.  

#### <a name="solution"></a>Lösning
Använd följande design med en separat egenskap för att lagra antalet meddelanden i varje timme:  

![Message stats-entitet med avgränsade egenskaper][23]

Med den här designen kan du använda en sammanslagnings åtgärd för att uppdatera antalet meddelanden för en medarbetare under en angiven timme. Nu kan du hämta all information som du behöver för att rita diagrammet med en begäran om en enda entitet.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Om din fullständiga data serie inte passar in i en enskild entitet (en entitet kan ha upp till 252 egenskaper) använder du ett alternativt data lager, till exempel en blob.  
* Om du har flera klienter som uppdaterar en entitet samtidigt måste du använda **etag** för att implementera optimistisk samtidighet. Om du har många klienter kan du uppleva hög konkurrens.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver uppdatera och hämta en data serie som är associerad med en enskild entitet.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Mönster för stora entiteter](#large-entities-pattern)  
* [Sammanfoga eller Ersätt](#merge-or-replace)  
* [Eventuellt konsekventa transaktions mönster](#eventually-consistent-transactions-pattern) (om du lagrar data serien i en BLOB)  

### <a name="wide-entities-pattern"></a>Mönster för breda entiteter
Använd flera fysiska entiteter för att lagra logiska entiteter med fler än 252 egenskaper.  

#### <a name="context-and-problem"></a>Kontext och problem
En enskild entitet får ha högst 252 egenskaper (exklusive de obligatoriska system egenskaperna) och kan inte lagra mer än 1 MB data totalt. I en Relations databas får du normalt en runda av eventuella gränser för storleken på en rad genom att lägga till en ny tabell och tvinga en 1-till-1-relation mellan dem.  

#### <a name="solution"></a>Lösning
Med hjälp av Table service kan du lagra flera entiteter som representerar ett enda stort företags objekt med fler än 252 egenskaper. Om du till exempel vill lagra antalet snabb meddelanden som skickats av varje medarbetare under de senaste 365 dagarna kan du använda följande design som använder två entiteter med olika scheman:  

![Message stats-entitet med Rowkey 01-och Message State-entitet med Rowkey 02][24]

Om du behöver göra en ändring som måste uppdatera båda enheterna för att synkronisera dem med varandra, kan du använda en EGT. Annars kan du använda en enda sammanslagnings åtgärd för att uppdatera antalet meddelanden för en viss dag. Om du vill hämta alla data för en enskild medarbetare måste du hämta båda entiteterna, som du kan utföra med två effektiva begär Anden som använder både ett **PartitionKey** och ett **RowKey** -värde.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Hämtning av en fullständig logisk entitet omfattar minst två lagrings transaktioner: en för att hämta varje fysisk enhet.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra entiteter vars storlek eller antal egenskaper överskrider gränserna för en enskild entitet i Table service.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Enhets grupp transaktioner](#entity-group-transactions)
* [Sammanfoga eller Ersätt](#merge-or-replace)

### <a name="large-entities-pattern"></a>Mönster för stora entiteter
Använd Blob Storage för att lagra stora egenskaps värden.  

#### <a name="context-and-problem"></a>Kontext och problem
En enskild entitet kan inte lagra mer än 1 MB data totalt. Om en eller flera av dina egenskaper lagrar värden som leder till att den totala storleken på din entitet överskrider det här värdet kan du inte lagra hela entiteten i Table service.  

#### <a name="solution"></a>Lösning
Om din enhet överskrider 1 MB storlek eftersom en eller flera egenskaper innehåller stora mängder data, kan du lagra data i Blob Service och sedan lagra adressen för blobben i en egenskap i entiteten. Du kan till exempel lagra fotot av en medarbetare i Blob Storage och lagra en länk till fotot i egenskapen **Photo** i din personal-entitet:  

![Anställd entitet med en sträng för foto som pekar på Blob Storage][25]

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Om du vill behålla eventuell konsekvens mellan entiteten i Table service och data i Blob Service använder du det slutliga [transaktions mönstret](#eventually-consistent-transactions-pattern) för att underhålla dina entiteter.
* Hämtning av en fullständig entitet omfattar minst två lagrings transaktioner: en för att hämta entiteten och en för att hämta BLOB-data.  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Använd det här mönstret när du behöver lagra entiteter vars storlek överskrider gränserna för en enskild entitet i Table service.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Mönster för eventuellt konsekventa transaktioner](#eventually-consistent-transactions-pattern)  
* [Mönster för breda entiteter](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Lägga/Lägg till anti-mönster
Öka skalbarheten när du har en stor mängd med infogningar genom att sprida tilläggen över flera partitioner.  

#### <a name="context-and-problem"></a>Kontext och problem
Väntande eller lägga till entiteter i dina lagrade entiteter resulterar vanligt vis i att programmet lägger till nya entiteter till den första eller sista partitionen i en sekvens med partitioner. I det här fallet sker alla infogningar vid en specifik tidpunkt i samma partition, vilket skapar en hotspot som förhindrar att tabell tjänsten använder belastnings utjämning infogar på flera noder, och möjligen kan orsaka att ditt program når skalbarhets målen för partitionstabellen. Om du till exempel har ett program som loggar nätverks-och resurs åtkomst av medarbetare, kan en enhets struktur enligt nedan kunna resultera i att den aktuella timmens partition blir ett hotspot-område om transaktions volymen når skalbarhets målet för en enskild partition:  

![Anställd entitet][26]

#### <a name="solution"></a>Lösning
Följande alternativa Entity-struktur förhindrar ett hotspot-område på en viss partition som program loggar händelser:  

![Anställd entitet med RowKey som sammansatte året, månaden, dagen, timmen och händelse-ID][27]

Observera i det här exemplet hur både **PartitionKey** och **RowKey** är sammansatta nycklar. **PartitionKey** använder både avdelnings-och medarbetar-ID för att distribuera loggningen över flera partitioner.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska implementera mönstret:  

* Stöder den alternativa nyckel strukturen som används för att snabbt skapa aktiva partitioner på tillägg effektivt de frågor som klient programmet gör?  
* Innebär den förväntade volymen av transaktioner att du sannolikt når skalbarhets målen för en enskild partition och att den begränsas av lagrings tjänsten?  

#### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret
Undvik lägga/Lägg till anti-mönstret när din volym av transaktioner sannolikt kommer att leda till en hastighet som begränsars av lagrings tjänsten när du använder en aktiv partition.  

#### <a name="related-patterns-and-guidance"></a>Relaterade mönster och vägledningar
Följande mönster och riktlinjer kan också vara relevanta när du implementerar det här mönstret:  

* [Sammansatt nyckel mönster](#compound-key-pattern)  
* [Logg änden-mönster](#log-tail-pattern)  
* [Ändra entiteter](#modifying-entities)  

### <a name="log-data-anti-pattern"></a>Kant mönster för logg data
Normalt bör du använda Blob Service i stället för Table service för att lagra loggdata.  

#### <a name="context-and-problem"></a>Kontext och problem
Ett vanligt användnings fall för loggdata är att hämta ett urval av logg poster för ett visst datum-/tidsintervall: du kan till exempel hitta alla fel och viktiga meddelanden som ditt program har loggat in mellan 15:04 och 15:06 på ett visst datum. Du vill inte använda datum och tid för logg meddelandet för att bestämma vilken partition du sparar log-entiteter på: det resulterar i en aktiv partition på grund av att alla log-entiteter delar samma **PartitionKey** -värde (se avsnittet [lägga/ Lägg till anti-mönster](#prepend-append-anti-pattern)). Följande enhets schema för ett logg meddelande resulterar till exempel i en aktiv partition eftersom programmet skriver alla logg meddelanden till partitionen för aktuellt datum och timme:  

![Logga meddelande enhet][28]

I det här exemplet inkluderar **RowKey** datum och tid för logg meddelandet för att säkerställa att logg meddelanden lagras sorterade i datum-/tids ordning och innehåller ett meddelande-ID om flera logg meddelanden delar samma datum och tid.  

En annan metod är att använda en **PartitionKey** som garanterar att programmet skriver meddelanden över flera olika partitioner. Om källan till logg meddelandet till exempel är ett sätt att distribuera meddelanden över flera partitioner, kan du använda följande enhets schema:  

![Logga meddelande enhet][29]

Men problemet med det här schemat är att hämta alla logg meddelanden under en angiven tids period. du måste söka igenom alla partitioner i tabellen.

#### <a name="solution"></a>Lösning
I föregående avsnitt har du markerat problemet med att försöka att använda Table service för att lagra logg poster och föreslagna två, otillfredsställande och design. En lösning ledde till en aktiv partition med risken för dåliga prestanda vid skrivning av logg meddelanden. den andra lösningen resulterade i dåliga fråge prestanda på grund av kravet på att söka igenom varje partition i tabellen för att hämta logg meddelanden för ett särskilt tidsintervall. Blob Storage erbjuder en bättre lösning för den här typen av scenario och det här är hur Azure-lagringsanalys lagrar de loggdata som samlas in.  

Det här avsnittet beskriver hur Lagringsanalys lagrar loggdata i Blob Storage som en illustration av den här metoden för att lagra data som du normalt frågar efter intervall.  

Lagringsanalys lagrar logg meddelanden i ett avgränsat format i flera blobbar. Det avgränsade formatet gör det enkelt för ett klient program att parsa data i logg meddelandet.  

Lagringsanalys använder en namngivnings konvention för blobbar som gör att du kan hitta bloben (eller blobbar) som innehåller de logg meddelanden som du söker efter. Till exempel innehåller en blob med namnet "Queue/2014/07/31/1800/000001. log" logg meddelanden som relaterar till Queue Service för timme från 18:00 den 31 juli 2014. "000001" visar att det här är den första logg filen för den här perioden. Lagringsanalys registrerar också tidsstämplar för de första och sista logg meddelandena som lagras i filen som en del av blobens metadata. Med API: et för Blob Storage kan du hitta blobbar i en behållare baserat på ett namn prefix: om du vill hitta alla blobbar som innehåller kös logg data för timmen från och med 18:00 kan du använda prefixet "Queue/2014/07/31/1800".  

Lagringsanalys buffrar logg meddelanden internt och uppdaterar sedan regelbundet rätt BLOB eller skapar en ny med den senaste batchen logg poster. Detta minskar antalet skrivningar som måste utföras till Blob-tjänsten.  

Om du implementerar en liknande lösning i ditt eget program måste du överväga hur du ska hantera kompromisser mellan pålitlighet (skriva varje loggpost till blob-lagringen) och kostnad och skalbarhet (buffrar uppdateringar i ditt program och skriver dem till Blob Storage i batchar.  

#### <a name="issues-and-considerations"></a>Problem och överväganden
Tänk på följande när du bestämmer hur du ska lagra loggdata:  

* Om du skapar en tabell design som gör det möjligt att undvika aktiva partitioner, kanske du upptäcker att du inte kan komma åt dina data på ett effektivt sätt.  
* För att bearbeta loggdata behöver en klient ofta läsa in många poster.  
* Även om loggdata ofta är strukturerade kan Blob Storage vara en bättre lösning.  

### <a name="implementation-considerations"></a>Att tänka på vid implementering
I det här avsnittet beskrivs några överväganden som du bör tänka på när du implementerar mönstren som beskrivs i föregående avsnitt. De flesta av det här avsnittet använder exempel C# skrivna i som använder lagrings klient biblioteket (version 4.3.0 vid tidpunkten för skrivning).  

### <a name="retrieving-entities"></a>Entiteter hämtas
Som det beskrivs i avsnittet [design för frågor](#design-for-querying), är den mest effektiva frågan en punkt fråga. I vissa fall kan du dock behöva hämta flera entiteter. I det här avsnittet beskrivs några vanliga metoder för att hämta entiteter med hjälp av lagrings klient biblioteket.  

#### <a name="executing-a-point-query-using-the-storage-client-library"></a>Köra en punkt fråga med lagrings klient biblioteket
Det enklaste sättet att köra en punkt fråga är att använda åtgärden **Hämta** tabell som visas i följande C# kodfragment som hämtar en entitet med en **PartitionKey** med värdet "Sales" och en **RowKey** med värdet "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Observera hur det här exemplet förväntar sig att entiteten hämtas till typen **EmployeeEntity**.  

#### <a name="retrieving-multiple-entities-using-linq"></a>Hämtar flera entiteter med LINQ
Du kan hämta flera entiteter med hjälp av LINQ med lagrings klient biblioteket och ange en fråga med en **WHERE** -sats. För att undvika en tabells ökning bör du alltid inkludera värdet **PartitionKey** i WHERE-satsen och om möjligt **RowKey** -värdet för att undvika tabell-och partitions ökningar. Tabell tjänsten har stöd för en begränsad uppsättning jämförelse operatorer (större än, större än eller lika med, mindre än, mindre än eller lika med, lika med och inte lika med) som ska användas i WHERE-satsen. Följande C# kodfragment söker efter alla anställda vars efter namn börjar med "B" (förutsatt att **RowKey** lagrar efter namn) på försäljnings avdelningen (förutsatt att **PartitionKey** lagrar avdelnings namnet):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Observera hur frågan anger både en **RowKey** och en **PartitionKey** för att få bättre prestanda.  

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
> Exemplet kapslar flera **CombineFilters** -metoder för att inkludera de tre filter villkoren.  
> 
> 

#### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Hämtar ett stort antal entiteter från en fråga
En optimal fråga returnerar en enskild entitet baserat på ett **PartitionKey** -värde och ett **RowKey** -värde. I vissa fall kan du dock ha ett krav för att returnera många entiteter från samma partition eller till och med från flera partitioner.  

Du bör alltid testa programmets prestanda fullständigt i sådana scenarier.  

En fråga mot tabell tjänsten kan returnera högst 1 000 entiteter vid ett tillfälle och kan köras i högst fem sekunder. Om resultatet innehåller fler än 1 000 entiteter, om frågan inte hade slutförts inom fem sekunder, eller om frågan korsar partitionens gränser, returnerar Table service en fortsättnings-token för att tillåta att klient programmet begär nästa uppsättning entiteter. Mer information om hur du använder fortsättnings-token finns i [fråga om tids gräns och sid brytning](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Om du använder lagrings klient biblioteket kan det automatiskt hantera fortsättnings-token åt dig när det returnerar entiteter från Table service. Följande C# kod exempel som använder lagrings klient biblioteket hanterar automatiskt fortsättnings-token om tabell tjänsten returnerar dem i ett svar:  

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

Följande C# kod hanterar fortsättnings-token uttryckligen:  

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

Genom att använda tilläggs-token explicit kan du styra när programmet hämtar nästa data segment. Om ditt klient program till exempel gör det möjligt för användare att gå igenom de entiteter som lagras i en tabell, kan en användare välja att inte gå igenom alla entiteter som hämtats av frågan så att ditt program bara använder en fortsättnings-token för att hämta nästa segment när användaren hade slut på växling genom alla entiteter i det aktuella segmentet. Den här metoden har flera fördelar:  

* Du kan begränsa mängden data som hämtas från Table service och som du flyttar över nätverket.  
* Med den kan du utföra asynkron IO i .NET.  
* Det gör att du kan serialisera fortsättnings-token till beständigt lagrings utrymme så att du kan fortsätta i händelse av en program krasch.  

> [!NOTE]
> En fortsättnings-token returnerar vanligt vis ett segment som innehåller 1 000 entiteter, även om det kan vara färre. Detta är också fallet om du begränsar antalet poster som en fråga returnerar genom att använda **ta** för att returnera de första n entiteter som matchar dina Sök villkor: tabell tjänsten kan returnera ett segment som innehåller färre än n entiteter och en fortsättnings-token till gör att du kan hämta återstående entiteter.  
> 
> 

Följande C# kod visar hur du ändrar antalet entiteter som returneras i ett segment:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projektion på Server Sidan
En enskild entitet kan ha upp till 255 egenskaper och vara upp till 1 MB. När du frågar tabellen och hämtar entiteter kanske du inte behöver alla egenskaper och kan undvika att överföra data i onödan (för att minska svars tid och kostnader). Du kan använda projektion på Server sidan för att bara överföra de egenskaper du behöver. I följande exempel hämtas bara **e-** postegenskapen (tillsammans med **PartitionKey**, **RowKey**, **timestamp**och **etag**) från de entiteter som valts av frågan.  

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

Observera hur **RowKey** -värdet är tillgängligt även om det inte finns med i listan över egenskaper som ska hämtas.  

### <a name="modifying-entities"></a>Ändra entiteter
Med lagrings klient biblioteket kan du ändra dina entiteter som lagras i tabell tjänsten genom att infoga, ta bort och uppdatera entiteter. Du kan använda EGTs för att utföra flera infognings-, uppdaterings-och borttagnings åtgärder tillsammans för att minska antalet fördröjningar som krävs och förbättra lösningens prestanda.  

Undantag som har utlösts när lagrings klient biblioteket kör en EGT inkluderar vanligt vis indexet för den entitet som gjorde att batchen kunde köras. Detta är användbart när du felsöker kod som använder EGTs.  

Du bör också fundera över hur din design påverkar hur ditt klient program hanterar samtidighets-och uppdaterings åtgärder.  

#### <a name="managing-concurrency"></a>Hantera samtidighet
Som standard implementerar tabell tjänsten optimistisk concurrency-kontroller på nivån för enskilda entiteter för **insert**-, **merge**-och **Delete** -åtgärder, även om det är möjligt för en klient att tvinga tabell tjänsten att kringgå dessa kontrol. Mer information om hur tabell tjänsten hanterar samtidighet finns i [Hantera samtidighet i Microsoft Azure Storage](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Sammanfoga eller Ersätt
**Ersättnings** metoden i **TableOperation** -klassen ersätter alltid den fullständiga entiteten i Table service. Om du inte tar med en egenskap i begäran när egenskapen finns i den lagrade entiteten tar begäran bort egenskapen från den lagrade entiteten. Om du inte vill ta bort en egenskap uttryckligen från en lagrad entitet måste du inkludera varje egenskap i begäran.  

Du kan använda **sammanslagnings** metoden för **TableOperation** -klassen för att minska mängden data som du skickar till den Table service när du vill uppdatera en entitet. **Sammanslagnings** metoden ersätter alla egenskaper i den lagrade entiteten med egenskaps värden från entiteten som ingår i begäran, men lämnar kvar egenskaper i den lagrade entiteten som inte ingår i begäran. Detta är användbart om du har stora entiteter och bara behöver uppdatera ett litet antal egenskaper i en begäran.  

> [!NOTE]
> Metoderna **replace** och **merge** Miss fungerar om entiteten inte finns. Alternativt kan du använda metoderna **InsertOrReplace** och **InsertOrMerge** som skapar en ny entitet om den inte finns.  
> 
> 

### <a name="working-with-heterogeneous-entity-types"></a>Arbeta med heterogena entitetstyper
Table service är en *schema lös* tabell lagring som innebär att en enskild tabell kan lagra entiteter av flera typer som ger stor flexibilitet i din design. Följande exempel illustrerar en tabell som lagrar både anställda och avdelnings enheter:  

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

Varje entitet måste fortfarande ha **PartitionKey**-, **RowKey**-och **timestamp** -värden, men kan ha en uppsättning egenskaper. Det finns dessutom inget som anger typen av entitet om du inte väljer att lagra informationen någonstans. Det finns två alternativ för att identifiera enhets typen:  

* Lägga av entitetstypen till **RowKey** (eller eventuellt **PartitionKey**). Till exempel **EMPLOYEE_000123** eller **DEPARTMENT_SALES** som **RowKey** -värden.  
* Använd en separat egenskap för att registrera entitetstypen som visas i tabellen nedan.  

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
<td>Medarbetar</td>
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
<td>Medarbetar</td>
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
<th>FirstName</th>
<th>LastName</th>
<th>Ålder</th>
<th>E-post</th>
</tr>
<tr>
<td>Medarbetar</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Det första alternativet, beroende på enhets typ till **RowKey**, är användbart om det finns en möjlighet att två entiteter av olika typer kan ha samma nyckel värde. Den grupperar också entiteter av samma typ tillsammans i partitionen.  

De metoder som beskrivs i det här avsnittet är särskilt relevanta för diskussions [arvs relationerna](#inheritance-relationships) tidigare i den här guiden i avsnittet modellerings relationer.  

> [!NOTE]
> Du bör överväga att inkludera ett versions nummer i enhets typens värde om du vill att klient program ska kunna utveckla POCO-objekt och arbeta med olika versioner.  
> 
> 

I resten av det här avsnittet beskrivs några av funktionerna i lagrings klient biblioteket som underlättar arbetet med flera entitetstyper i samma tabell.  

#### <a name="retrieving-heterogeneous-entity-types"></a>Hämtar heterogena entitetstyper
Om du använder lagrings klient biblioteket har du tre alternativ för att arbeta med flera olika entitetstyper.  

Om du vet vilken typ av entitet som lagras med ett särskilt värde för **RowKey** och **PartitionKey** kan du ange entitetstypen när du hämtar entiteten som du ser i föregående två exempel som hämtar entiteter av typen **EmployeeEntity** : [Köra en punkt fråga med lagrings klient biblioteket](#executing-a-point-query-using-the-storage-client-library) och [Hämta flera entiteter med LINQ](#retrieving-multiple-entities-using-linq).  

Det andra alternativet är att använda **DynamicTableEntity** -typen (en egenskaps uppsättning) i stället för en konkret Poco-entitetstyp (det här alternativet kan också förbättra prestanda eftersom det inte behövs att serialisera och deserialisera entiteten till .net-typer). Följande C# kod kan användas för att hämta flera entiteter av olika typer från tabellen, men returnerar alla entiteter som **DynamicTableEntity** -instanser. Den använder sedan egenskapen **EntityType** för att fastställa typen av varje entitet:  

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

Om du vill hämta andra egenskaper måste du använda metoden **TryGetValue** i egenskapen **Properties** för klassen **DynamicTableEntity** .  

Ett tredje alternativ är att kombinera med **DynamicTableEntity** -typen och en **EntityResolver** -instans. På så sätt kan du matcha flera POCO-typer i samma fråga. I det här exemplet använder **EntityResolver** -delegaten egenskapen **EntityType** för att skilja mellan de två typerna av entiteter som frågan returnerar. **Lösnings** metoden använder **lösare** ombudet för att lösa **DynamicTableEntity** -instanser till **TableEntity** -instanser.  

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

#### <a name="modifying-heterogeneous-entity-types"></a>Ändra typer av heterogena entiteter
Du behöver inte känna till typen av en entitet för att ta bort den och du vet alltid vilken typ av enhet som används när du infogar den. Du kan dock använda **DynamicTableEntity** -typ för att uppdatera en entitet utan att känna till dess typ och utan att använda en Poco Entity-klass. I följande kod exempel hämtas en enskild entitet och kontrollerar att egenskapen **EmployeeCount** finns innan den uppdateras.  

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

### <a name="controlling-access-with-shared-access-signatures"></a>Kontrol lera åtkomst med signaturer för delad åtkomst
Du kan använda signaturer för delad åtkomst (SAS) för att göra det möjligt för klient program att ändra (och fråga) tabell enheter direkt utan att behöva autentisera direkt med tabell tjänsten. Det finns vanligt vis tre viktiga fördelar med att använda SAS i ditt program:  

* Du behöver inte distribuera lagrings konto nyckeln till en osäker plattform (till exempel en mobil enhet) för att tillåta att enheten får åtkomst till och ändrar entiteter i Table service.  
* Du kan avlasta en del av arbetet som Web-och Worker-roller utför vid hantering av dina entiteter på klient enheter som slutanvändares datorer och mobila enheter.  
* Du kan tilldela en begränsad behörighet och begränsad tid till en klient (till exempel tillåta skrivskyddad åtkomst till särskilda resurser).  

Mer information om hur du använder SAS-token med Table service finns i [använda signaturer för delad åtkomst (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Du måste dock fortfarande generera SAS-token som ger ett klient program till entiteterna i tabell tjänsten: gör detta i en miljö som har säker åtkomst till dina lagrings konto nycklar. Normalt använder du en webb-eller arbets roll för att generera SAS-token och leverera dem till de klient program som behöver åtkomst till dina entiteter. Eftersom det fortfarande finns en omkostnader som används för att skapa och leverera SAS-token till klienter, bör du överväga hur du bäst kan minska den här belastningen, särskilt i stora volym scenarier.  

Det går att skapa en SAS-token som beviljar åtkomst till en delmängd av entiteterna i en tabell. Som standard skapar du en SAS-token för en hel tabell, men du kan också ange att SAS-token beviljar åtkomst till antingen ett intervall med **PartitionKey** -värden eller ett intervall med **PartitionKey** -och **RowKey** -värden. Du kan välja att generera SAS-token för enskilda användare av systemet, så att varje användares SAS-token endast tillåter åtkomst till sina egna entiteter i tabell tjänsten.  

### <a name="asynchronous-and-parallel-operations"></a>Asynkrona och parallella åtgärder
Förutsatt att du sprider dina begär anden över flera partitioner kan du förbättra genomflödet och klientens svars tider med hjälp av asynkrona eller parallella frågor.
Du kan till exempel ha två eller flera arbets Rolls instanser som har åtkomst till dina tabeller parallellt. Du kan ha enskilda arbets roller som är ansvariga för specifika uppsättningar av partitioner eller bara ha flera arbets Rolls instanser, och varje användare kan komma åt alla partitioner i en tabell.  

Inom en klient instans kan du förbättra data flödet genom att köra lagrings åtgärder asynkront. Med lagrings klient biblioteket är det enkelt att skriva asynkrona frågor och ändringar. Du kan till exempel börja med den synkrona metoden som hämtar alla entiteter i en partition som visas i följande C# kod:  

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

Du kan enkelt ändra koden så att frågan körs asynkront på följande sätt:  

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

* Metodsignaturen innehåller nu den **asynkrona** modifieraren och returnerar en **uppgifts** instans.  
* I stället för att anropa **ExecuteSegmented** -metoden för att hämta resultat anropar metoden **ExecuteSegmentedAsync** -metoden och använder modifieraren **await** för att hämta resultat asynkront.  

Klient programmet kan anropa den här metoden flera gånger (med olika värden för parametern **Department** ) och varje fråga kommer att köras i en separat tråd.  

Det finns ingen asynkron version av **execute** -metoden i klassen **TableQuery** eftersom **IEnumerable** -gränssnittet inte stöder asynkron uppräkning.  

Du kan också infoga, uppdatera och ta bort entiteter asynkront. I följande C# exempel visas en enkel, synkron metod för att infoga eller ersätta en anställds entitet:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Du kan enkelt ändra koden så att uppdateringen körs asynkront på följande sätt:  

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

* Metodsignaturen innehåller nu den **asynkrona** modifieraren och returnerar en **uppgifts** instans.  
* I stället för att anropa metoden **execute** för att uppdatera entiteten anropar metoden **ExecuteAsync** -metoden och använder modifieraren **await** för att hämta resultat asynkront.  

Klient programmet kan anropa flera asynkrona metoder som det här, och varje metod anrop körs i en separat tråd.  

### <a name="credits"></a>Krediter
Vi vill tacka nej till följande medlemmar i Azure-teamet för sina bidrag: Dominic Betts, Jason Hogg, Jean Ghanem, Jai Haridas, Jeff Irwin, Vamshidhar kommineni, Vinay Shah och Serdar Ozler samt tom Hollander från Microsoft DX. 

Vi skulle också vilja tacka följande Microsoft-MVP: er för deras värdefulla feedback under gransknings cykler: Igor Papirov och Edward Bakker.

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


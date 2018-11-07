---
title: Utforma skalbara och högpresterande tabeller i Azure table storage. | Microsoft Docs
description: Utforma skalbara och högpresterande tabeller i Azure table storage.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: c5b18bce9d0cf78569d0c2fa02ad14c96ad09bd1
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51237782"
---
# <a name="design-scalable-and-performant-tables"></a>Utforma skalbara och högpresterande tabeller

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Att utforma skalbara och högpresterande tabeller, måste du tänka på faktorer som prestanda, skalbarhet och kostnad. Om du har tidigare designat scheman för relationsdatabaser, dessa överväganden är bekant, men det finns vissa likheter mellan Azure Table storage modell och relationella modeller, men det finns också viktiga skillnader. Dessa skillnader vanligtvis leda till olika designer som kan se ut krånglig eller fel till någon som är bekant med relationsdatabaser, men meningsfullt om du designar för ett NoSQL nyckel/värde-lager, till exempel Azure Table service. Många av din design skillnader återspeglar det faktum att Table service är utformad för att stödja skalbar program som kan innehålla miljarder entiteter (eller rader i en relationsdatabas terminologi) av data eller för datauppsättningar som måste ha stöd för hög transaktion volymer. Därför måste du tänka på olika sätt hur du lagrar data och förstå hur tabelltjänsten fungerar. En väl utformad NoSQL-databas kan aktivera din lösning för att skala och mycket ytterligare till en lägre kostnad än en lösning som använder en relationsdatabas. Den här guiden hjälper dig med de här avsnitten.  

## <a name="about-the-azure-table-service"></a>Om Azure Table service
Det här avsnittet beskrivs några av de viktigaste funktionerna i Table service som är särskilt relevanta till utformning för prestanda och skalbarhet. Om du inte har använt Azure Storage eller Table service, läser du först [introduktion till Microsoft Azure Storage](../../storage/common/storage-introduction.md) och [Kom igång med Azure Table Storage med hjälp av .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) innan du läser resten av den här artikeln . Även om den här guiden fokuserar på är i tabelltjänsten, innehåller beskrivning av de Azure-kö och Blob-tjänsterna och hur du kan använda dem med Table service.  

Vad är tabelltjänsten? Som förväntat från namnet, använder tabelltjänsten tabellformat för att lagra data. Varje rad i tabellen representerar en entitet i standard-terminologi och kolumnerna lagra de olika egenskaperna för denna entitet. Varje entitet har ett par nycklar för unik identifiering och en kolumn för tidsstämpling Table service använder för att spåra när entiteten senast uppdaterades. Tidsstämpeln tillämpas automatiskt och du kan inte skriva över tidsstämpeln manuellt med ett godtyckligt värde. Table service använder den här senaste ändring tidsstämpeln (LMT) för att hantera Optimistisk samtidighet.  

> [!NOTE]
> Table service REST API-åtgärder kan också returnera en **ETag** värdet som den härleds från LMT. Det här dokumentet används villkoren ETag och LMT synonymt eftersom de refererar till samma underliggande data.  
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


Dessa data visas hittills liknar en tabell i en relationsdatabas med de viktigaste skillnaderna är de obligatoriska kolumnerna och möjlighet att lagra flera typer av enheter i samma tabell. Dessutom var och en av de användardefinierade egenskaperna som **FirstName** eller **ålder** har en datatyp som heltal eller ett strängvärde, precis som en kolumn i en relationsdatabas. Men till skillnad från i en relationsdatabas, Table service utan schema natur innebär att en egenskap inte behöver ha samma datatyp för varje entitet. För att lagra komplexa datatyper i en enskild egenskap, måste du använda ett serialiserade format som JSON eller XML. Läs mer om tabelldatatyper för tjänsten som stöds, stöds datumintervall, namngivningsregler och begränsningar för storlek, [förstå den tabelltjänst-datamodellen](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Ditt val av **PartitionKey** och **RowKey** är nyckeln till bra tabelldesign. Alla entiteter som lagras i en tabell måste ha en unik kombination av **PartitionKey** och **RowKey**. Precis som med nycklar i en relationsdatabas-tabell, den **PartitionKey** och **RowKey** värden indexeras för att skapa ett grupperat index om du vill aktivera snabb look-ups. Dock tabelltjänsten skapar inte sekundära index, så **PartitionKey** och **RowKey** är de enda indexerade egenskaperna. Några av de mönster som beskrivs i [tabell designmönster](table-storage-design-patterns.md) illustrerar hur du kan undvika den här tydligt begränsning.  

En tabell består av en eller flera partitioner och många av de designbesluten kommer att välja en lämplig **PartitionKey** och **RowKey** att optimera din lösning. En lösning kan bestå av en enda tabell som innehåller alla dina entiteter som är uppdelad i partitioner, men vanligtvis en lösning har flera tabeller. Tabeller hjälper dig att organisera dina entiteter, hjälper dig att hantera åtkomst till data med hjälp av åtkomstkontrollistor logiskt och du kan släppa en hel tabell med en enda lagringsåtgärd.  

## <a name="table-partitions"></a>Tabellpartitioner
Kontonamn, tabell och **PartitionKey** tillsammans identifierar partitionen i lagringstjänsten där tabelltjänsten sparar entiteten. Förutom att en del av adresseringsschema för entiteter, partitioner för att definiera ett omfång för transaktioner (se [Entitetsgrupptransaktioner](#entity-group-transactions) nedan), och utgör grunden för hur tabelltjänsten skalas. Läs mer på partitioner, [skalbarhet för lagring av Azure- och prestandamål](../../storage/common/storage-scalability-targets.md).  

I Table service, en enskild nod tjänst eller mer Slutför partitioner och skalor för tjänsten genom att dynamiskt belastningsutjämning partitionerna mellan noder. Om en nod är under belastning, table service kan *dela* antal partitioner som underhålls av noden till andra noder; när trafik löst, kan tjänsten *merge* partitionsintervall från tyst noder tillbaka till en enda nod.  

Mer information om de interna detaljerna för tabelltjänsten, särskilt hur tjänsten hanterar partitionerna, finns i dokumentet [Microsoft Azure Storage: A med hög Available Cloud Storage Service with Strong Consistency](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

## <a name="entity-group-transactions"></a>Entitetsgrupptransaktioner
I tabelltjänsten är Entitetsgrupptransaktioner (EGTs) bara inbyggd mekanism för att utföra atomiska uppdateringar i flera entiteter. EGTs ibland även kallas *batch transaktioner*. EGTs kan endast användas på entiteter som lagras i samma partition (det vill säga dela samma partitionsnyckel i en viss tabell). Så varje gång du behöver atomiska transaktionella beteende i flera entiteter kan du se till att dessa enheter finns i samma partition. Det här är ofta en orsak till att hålla flera typer av enheter i samma tabell (och partition) och inte använder flera tabeller för olika enhetstyper. En enda EGT tillämpas på högst 100 entiteter.  Om du skickar in flera samtidiga EGTs för bearbetning, är det viktigt att se till att dessa EGTs inte fungerar på entiteter som är gemensamma för EGTs; i annat fall kan bearbetning fördröjas.

EGTs medför också en potentiell kompromiss där du kan utvärdera i utformningen. Det vill säga ökar med fler partitioner skalbarheten i ditt program, eftersom Azure har fler möjligheter för förfrågningar för belastningsutjämning mellan noder. Men med fler partitioner kan begränsa möjligheten för programmet att utföra atomiska transaktioner och underhålla stark konsekvens för dina data. Det är dessutom specifika skalbarhetsmål på nivån för en partition som kan begränsa dataflödet för transaktioner som du kan förvänta dig för en enda nod. Läs mer om skalbarhetsmål för Azure storage-konton och table service, [skalbarhet för lagring av Azure- och prestandamål](../../storage/common/storage-scalability-targets.md).   

## <a name="capacity-considerations"></a>Överväganden för kapacitet
I följande tabell beskrivs några av nyckelvärden känna till när du utformar en lösning för tabellen:  

| Total kapacitet för ett Azure storage-konto | 500 TB |
| --- | --- |
| Antalet tabeller i ett Azure storage-konto |Begränsas bara av kapaciteten för storage-konto |
| Antalet partitioner i en tabell |Begränsas bara av kapaciteten för storage-konto |
| Antal entiteter i en partition |Begränsas bara av kapaciteten för storage-konto |
| Storleken på en enskild entitet |Upp till 1 MB med högst 255 egenskaper (inklusive den **PartitionKey**, **RowKey**, och **tidsstämpel**) |
| Storleken på den **PartitionKey** |En sträng upp till 1 KB stora |
| Storleken på den **RowKey** |En sträng upp till 1 KB stora |
| Storleken på en Entitetsgrupp-transaktion |En transaktion får innehålla högst 100 entiteter och nyttolasten måste vara mindre än 4 MB i storlek. En entitet kan bara uppdatera en gång i en EGT. |

Mer information finns i [Understanding the Table Service Data Model](https://msdn.microsoft.com/library/azure/dd179338.aspx) (Så här fungerar datamodellen för Table Storage).  

## <a name="cost-considerations"></a>Kostnadsöverväganden
Table storage är relativt prisvärda, men du bör innehålla kostnadsuppskattningar för både kapacitetsförbrukning och antalet transaktioner som en del av utvärderingen av valfri lösning för tabellen. I många fall kan är lagra Avnormaliserade eller duplicerade data för att förbättra prestanda och skalbarhet för din lösning dock en giltig metod. Mer information om priser finns i [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Nästa steg

- [Designmönster för tabell](table-storage-design-patterns.md)
- [Modellering relationer](table-storage-design-modeling.md)
- [Design för frågor](table-storage-design-for-query.md)
- [Kryptering av tabelldata som](table-storage-design-encrypt-data.md)
- [Design för dataändringar](table-storage-design-for-modification.md)
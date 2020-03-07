---
title: Utforma skalbara och presterande tabeller i Azure Table Storage. | Microsoft Docs
description: Utforma skalbara och presterande tabeller i Azure Table Storage.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 95272956da4567ec21e1c4603b88472e45373a39
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387143"
---
# <a name="design-scalable-and-performant-tables"></a>Utforma skalbara och högpresterande tabeller

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

För att utforma skalbara och presterande tabeller måste du tänka på faktorer som prestanda, skalbarhet och kostnad. Om du tidigare har utformat scheman för Relations databaser är dessa saker välkända, men det finns vissa likheter mellan Azure Table service Storage-modellen och Relations modeller, men det finns också viktiga skillnader. Dessa skillnader leder vanligt vis till olika design som kan se till att det är intuitivt eller fel i någon bekant relation mellan Relations databaser, men är avgörande om du designar för en NoSQL nyckel/värde-lagring, till exempel Azure Table service. Många av dina design skillnader återspeglar det faktum att Table service har utformats för att stödja molnbaserade program som kan innehålla miljarder entiteter (eller rader i Relations databas terminologi) eller för data uppsättningar som måste ha stöd för hög transaktion enheter. Därför måste du tänka på ett annat sätt att lagra dina data och förstå hur Table service fungerar. Ett väl utformat NoSQL-datalager kan göra det möjligt för din lösning att skala mycket mer och till en lägre kostnad än en lösning som använder en Relations databas. Den här guiden hjälper dig med de här avsnitten.  

## <a name="about-the-azure-table-service"></a>Om Azure Table service
Det här avsnittet beskrivs några av de viktigaste funktionerna i Table service som är särskilt relevanta till utformning för prestanda och skalbarhet. Om du är nybörjare på Azure Storage och Table service läser du först [Introduktion till Microsoft Azure Storage](../../storage/common/storage-introduction.md) och [kommer igång med Azure Table Storage med .net](../../cosmos-db/table-storage-how-to-use-dotnet.md) innan du läser resten av den här artikeln. Även om den här hand boken är på Table service, innehåller den även en diskussion om Azure-kö-och blob-tjänster och hur du kan använda dem med Table service.  

Vad är tabelltjänsten? Som förväntat från namnet, använder tabelltjänsten tabellformat för att lagra data. Varje rad i tabellen representerar en entitet i standard-terminologi och kolumnerna lagra de olika egenskaperna för denna entitet. Varje entitet har ett nyckel par för att unikt identifiera den och en tidsstämpelkolumn som Table service använder för att spåra när entiteten senast uppdaterades. Tidsstämpeln tillämpas automatiskt och du kan inte skriva över tidsstämpeln manuellt med ett godtyckligt värde. Table service använder den här senaste ändring tidsstämpeln (LMT) för att hantera Optimistisk samtidighet.  

> [!NOTE]
> Table service REST API-åtgärder returnerar också ett **etag** -värde som den härleds från LMT. Det här dokumentet använder termerna ETag och LMT, vilket är utbytbara eftersom de refererar till samma underliggande data.  
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


Hittills ser dessa data likadant ut som en tabell i en Relations databas med de viktigaste skillnaderna som de obligatoriska kolumnerna och möjligheten att lagra flera entitetstyper i samma tabell. Dessutom har var och en av de användardefinierade egenskaperna, till exempel **FirstName** eller **Age** , en datatyp, till exempel Integer eller string, precis som en kolumn i en Relations databas. Men till skillnad från i en relationsdatabas, Table service utan schema natur innebär att en egenskap inte behöver ha samma datatyp för varje entitet. För att lagra komplexa datatyper i en enskild egenskap, måste du använda ett serialiserade format som JSON eller XML. Mer information om tabell tjänsten, till exempel data typer som stöds, datum intervall som stöds, namngivnings regler och storleks begränsningar, finns i [förstå tabell tjänstens data modell](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Ditt val av **PartitionKey** och **RowKey** är grundläggande för en bra tabell design. Varje entitet som lagras i en tabell måste ha en unik kombination av **PartitionKey** och **RowKey**. Precis som med nycklar i en Relations databas tabell indexeras värdena för **PartitionKey** och **RowKey** för att skapa ett grupperat index för att aktivera snabb uppslags fönster. Table service skapar dock inga sekundära index, så **PartitionKey** och **RowKey** är de enda indexerade egenskaperna. Några av mönstren som beskrivs i [tabell design mönster](table-storage-design-patterns.md) illustrerar hur du kan undvika den här synliga begränsningen.  

En tabell består av en eller flera partitioner, och många av de design beslut du gör är att välja en lämplig **PartitionKey** och **RowKey** för att optimera din lösning. En lösning kan bestå av en enda tabell som innehåller alla entiteter som är ordnade i partitioner, men vanligt vis har en lösning flera tabeller. Tabeller hjälper dig att organisera dina entiteter, hjälper dig att hantera åtkomst till data med hjälp av åtkomstkontrollistor logiskt och du kan släppa en hel tabell med en enda lagringsåtgärd.  

## <a name="table-partitions"></a>Tabellpartitioner
Konto namnet, tabell namnet och **PartitionKey** tillsammans identifierar partitionen i lagrings tjänsten där tabell tjänsten lagrar entiteten. Som en del av adresserings planen för entiteter definierar partitionerna en omfattning för transaktioner (se [enhets grupp transaktioner](#entity-group-transactions) nedan) och utgör grunden för hur tabell tjänsten skalar. Mer information om partitioner finns i [Check lista för prestanda och skalbarhet för Table Storage](storage-performance-checklist.md).  

I Table service, en enskild nod som utför en eller flera fullständiga partitioner, och tjänsten skalas genom en dynamisk belastnings utjämning av partitioner mellan noder. Om en nod är under inläsning kan tabell tjänsten *dela* det intervall med partitioner som hanteras av noden på olika noder. När trafik under sidor, kan tjänsten *sammanfoga* partitionernas intervall från tysta noder tillbaka till en enda nod.  

Mer information om den interna informationen om Table service och i synnerhet hur tjänsten hanterar partitioner finns i pappers [Microsoft Azure Storage: en moln lagrings tjänst med hög tillgänglighet med stark konsekvens](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

## <a name="entity-group-transactions"></a>Entitetsgrupptransaktioner
I tabelltjänsten är Entitetsgrupptransaktioner (EGTs) bara inbyggd mekanism för att utföra atomiska uppdateringar i flera entiteter. EGTs kallas ibland även för batch- *transaktioner*. EGTs kan bara användas på entiteter som är lagrade i samma partition (det vill säga samma partitionsnyckel i en specifik tabell). Så varje gång du behöver Atomic-transaktionell beteende över flera entiteter måste du se till att dessa entiteter finns i samma partition. Det här är ofta en orsak till att hålla flera typer av enheter i samma tabell (och partition) och inte använder flera tabeller för olika enhetstyper. En enda EGT tillämpas på högst 100 entiteter.  Om du skickar flera samtidiga EGTs för bearbetning är det viktigt att se till att dessa EGTs inte fungerar på entiteter som är gemensamma för EGTs; Annars kan bearbetningen fördröjas.

EGTs introducerar också en potentiell kompromiss som du kan använda för att utvärdera i din design. Det innebär att om du använder fler partitioner ökar programmets skalbarhet eftersom Azure har fler möjligheter för belastnings Utjämnings begär Anden mellan noder. Men att använda fler partitioner kan begränsa möjligheten för ditt program att utföra atomiska transaktioner och upprätthålla stark konsekvens för dina data. Dessutom finns det vissa skalbara mål på nivån för en partition som kan begränsa data flödet för transaktioner som du kan förväntar dig för en enda nod. Mer information om skalbarhets mål för Azure standard Storage-konton finns i [skalbarhets mål för standard lagrings konton](../common/scalability-targets-standard-account.md). Mer information om skalbarhets mål för Table service finns i skalbarhets- [och prestanda mål för Table Storage](scalability-targets.md).

## <a name="capacity-considerations"></a>Överväganden för kapacitet
I följande tabell beskrivs några av de viktigaste värdena som du bör känna till när du skapar en Table service lösning:  

| Total kapacitet för ett Azure storage-konto | 500 TB |
| --- | --- |
| Antalet tabeller i ett Azure storage-konto |Begränsas bara av kapaciteten för storage-konto |
| Antalet partitioner i en tabell |Begränsas bara av kapaciteten för storage-konto |
| Antal entiteter i en partition |Begränsas bara av kapaciteten för storage-konto |
| Storleken på en enskild entitet |Upp till 1 MB med högst 255 egenskaper (inklusive **PartitionKey**, **RowKey**och **tidsstämpel**) |
| Storlek på **PartitionKey** |En sträng upp till 1 KB stora |
| Storlek på **RowKey** |En sträng upp till 1 KB stora |
| Storleken på en Entitetsgrupp-transaktion |En transaktion får innehålla högst 100 entiteter och nyttolasten måste vara mindre än 4 MB i storlek. En entitet kan bara uppdatera en gång i en EGT. |

Mer information finns i [Understanding the Table Service Data Model](https://msdn.microsoft.com/library/azure/dd179338.aspx) (Så här fungerar datamodellen för Table Storage).  

## <a name="cost-considerations"></a>Kostnadsöverväganden
Table Storage är relativt billigt, men du bör inkludera kostnads uppskattningar för både kapacitets användning och antalet transaktioner som en del av din utvärdering av en Table service lösning. I många fall är det dock ett giltigt tillvägagångs sätt att lagra denormaliserade eller duplicerade data för att förbättra prestandan eller skalbarheten för din lösning. Mer information om priser finns i [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Nästa steg

- [Tabell design mönster](table-storage-design-patterns.md)
- [Modellerings relationer](table-storage-design-modeling.md)
- [Design för frågor](table-storage-design-for-query.md)
- [Kryptera tabell data](table-storage-design-encrypt-data.md)
- [Design för data ändring](table-storage-design-for-modification.md)

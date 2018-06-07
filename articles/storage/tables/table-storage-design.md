---
title: Design skalbar och performant tabeller i Azure table storage. | Microsoft Docs
description: Design skalbar och performant tabeller i Azure table storage.
services: storage
documentationcenter: na
author: SnehaGunda
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: ba48283371ac099b162aeb3cbffd6127ccce1676
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34661046"
---
# <a name="design-scalable-and-performant-tables"></a>Utforma skalbara och högpresterande tabeller

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Att utforma skalbar och performant tabeller, måste du tänka på faktorer som prestanda, skalbarhet och kostnader. Om du har tidigare designat scheman för relationsdatabaser, dessa överväganden är bekant, men det finns vissa likheter mellan Azure Table storage modell och relationella modeller, men det finns också viktiga skillnader. Dessa skillnader vanligtvis leda till olika konstruktionerna som kan vara krånglig eller felaktig till någon bekant med relationsdatabaser ännu vara meningsfullt om du designar för en NoSQL nyckel/värde-arkivet, t.ex Azure Table-tjänsten. Många av din design skillnader bero på att tabelltjänsten är utformad för att stödja skalbar molnlagring program som kan innehålla miljarder entiteter (eller rader i en relationsdatabas terminologi) av data eller för datauppsättningar som måste ha stöd för hög transaktion volymer. Därför måste du tänka på olika sätt på hur du lagrar data och förstå hur tabelltjänsten fungerar. En väl utformad NoSQL-databas kan aktivera din lösning för att skala och mycket mer till en lägre kostnad än en lösning som använder en relationsdatabas. Den här guiden hjälper dig med dessa frågor.  

## <a name="about-the-azure-table-service"></a>Om tjänsten Azure Table
Det här avsnittet beskrivs några av de viktigaste funktionerna i tabelltjänsten som är särskilt relevanta till utformning för prestanda och skalbarhet. Om du har använt Azure Storage- och tabelltjänsten läser du först [introduktion till Microsoft Azure Storage](../../storage/common/storage-introduction.md) och [Kom igång med Azure Table Storage med hjälp av .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) innan du läser resten av den här artikeln . Även om den här guiden fokuserar på tabelltjänsten, innehåller beskrivning av Azure Queue och Blob-tjänster och hur du kan använda dem med tabelltjänsten.  

Vad är tabelltjänsten? Som du kan förvänta sig från namnet använder tabelltjänsten tabellformat för att lagra data. Varje rad i tabellen representerar en entitet i standard terminologi och kolumnerna lagra olika egenskaper för enheten. Varje entitet har ett par nycklar för att identifiera och en tidsstämpelkolumn som tabelltjänsten använder för att spåra när entiteten senast uppdaterades. Du kan inte skriva över tidsstämpeln manuellt med ett godtyckligt värde tidsstämpeln tillämpas automatiskt. Tabelltjänsten använder den här last-modified-tidsstämpel (LMT) för att hantera Optimistisk samtidighet.  

> [!NOTE]
> REST API för tabellen tjänståtgärderna också returnera en **ETag** värde som den härleds från LMT. Det här dokumentet använder villkoren ETag och LMT synonymt eftersom de hänvisar till samma underliggande data.  
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


Dessa data visas hittills liknar en tabell i en relationsdatabas med de viktigaste skillnaderna är de obligatoriska kolumnerna och möjlighet att lagra flera typer av enheter i samma tabell. Varje användardefinierade egenskaper som **Förnamn** eller **ålder** har en datatyp som heltal eller en sträng, precis som en kolumn i en relationsdatabas. Men till skillnad från i en relationsdatabas tabelltjänsten schemat mindre uppbyggnad innebär att en egenskap måste ha samma datatyp för varje entitet. Om du vill lagra komplexa datatyper i en enskild egenskap, måste du använda ett serialiserat format, till exempel JSON eller XML. Mer information om datatyper i tabellen service som stöds, stöds datumintervall, namngivningsregler och storlek restriktioner finns [förstå den tabelltjänst-datamodellen](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Ditt val av **PartitionKey** och **RowKey** är grundläggande för bra tabelldesign. Varje entitet som lagras i en tabell måste ha en unik kombination av **PartitionKey** och **RowKey**. Precis som med nycklar i en relationsdatabas tabell den **PartitionKey** och **RowKey** värden indexeras om du vill skapa ett grupperat index om du vill aktivera snabb look-ups. Dock tabelltjänsten skapar inte någon sekundärindex så **PartitionKey** och **RowKey** är de enda indexerade egenskaperna. Några av de mönster som beskrivs i [tabell designmönster](table-storage-design-patterns.md) illustrerar hur du kan undvika den här tydligt begränsningen.  

En tabell består av en eller flera partitioner och många designbeslut som du kommer att runt att välja en lämplig **PartitionKey** och **RowKey** att optimera din lösning. En lösning kan bestå av en enda tabell som innehåller alla entiteter indelade i partitioner, men vanligtvis en lösning innehåller flera tabeller. Tabeller hjälper dig att ordna dina enheter, hjälper dig att hantera åtkomst till data med åtkomstkontrollistor logiskt och du kan släppa en hel tabell med hjälp av en enda lagring-åtgärd.  

## <a name="table-partitions"></a>Tabellpartitioner
Kontonamn, tabellnamn, och **PartitionKey** identifierar tillsammans partitionen i lagringstjänsten där tabelltjänsten lagrar entiteten. Samt som en del av adresseringsschema för entiteter partitioner för att definiera ett omfång för transaktioner (se [entitet gruppera transaktioner](#entity-group-transactions) nedan), och utgör grunden för hur tabelltjänsten skalas. Mer information om partitioner finns [Azure Storage skalbarhets- och prestandamål](../../storage/common/storage-scalability-targets.md).  

I tabellen service är en enskild nod tjänsterna eller mer Slutför partitioner och tjänsten skalor av dynamiskt nätverksbelastning partitioner mellan noder. Om en nod under belastning kan tabelltjänsten *dela* antal partitioner som underhålls av noden på olika noder; när trafik subsides tjänsten kan *merge* partition intervall från tyst noder tillbaka till en enda nod.  

Mer information om internt information för tabelltjänsten, särskilt hur tjänsten hanterar partitioner, finns i dokumentet [Microsoft Azure Storage: A hög Available Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

## <a name="entity-group-transactions"></a>Entiteten gruppera transaktioner
I tjänsten tabell är entitet grupptransaktioner (EGTs) den inbyggda mekanismen för att utföra atomiska uppdateringar över flera enheter. EGTs ibland också kallas *batch transaktioner*. EGTs fungerar bara med entiteter som lagras i samma partition (det vill säga dela samma partitionsnyckel i en given tabell). Så att varje gång som du behöver atomiska transaktionella beteende över flera enheter, måste du säkerställa att dessa enheter finns i samma partition. Det här är ofta en orsak till att ha flera typer av enheter i samma tabell (och partition) och inte använder flera tabeller för olika enhetstyper. En enda EGT kan tillämpas på högst 100 entiteter.  Om du skickar flera samtidiga EGTs för bearbetning, är det viktigt att se till att dessa EGTs inte fungerar på enheter som är gemensamma för EGTs; Annars kan du vänta bearbetning.

EGTs också introducera en potentiell kompromiss där du kan utvärdera i din design. Det vill säga ökar med fler partitioner skalbarhet i ditt program eftersom Azure har flera möjligheter för belastningsutjämning begäranden mellan noder. Men med fler partitioner kan begränsa möjligheten att utföra atomiska transaktioner och underhålla stark konsekvens för dina data. Dessutom finns specifika skalbarhetsmål på nivån för en partition som kan begränsa genomflödet av transaktioner som du kan förvänta dig för en enda nod. Läs mer om skalbarhetsmål för Azure storage-konton och tabelltjänsten [Azure Storage skalbarhets- och prestandamål](../../storage/common/storage-scalability-targets.md).   

## <a name="capacity-considerations"></a>Överväganden för kapacitetsplanering
Följande tabell beskriver vissa av nyckelvärden för att vara medveten om när du designar en lösning för tabell:  

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

## <a name="cost-considerations"></a>Kostnad överväganden
Table storage är relativt billig, men du bör innehålla kostnaderna för både kapacitetsförbrukning och antalet transaktioner som en del av din utvärderingsversion av någon lösning för tabellen. I många fall kan är lagra Avnormaliserade eller duplicerade data för att förbättra prestanda och skalbarhet för din lösning dock en giltig metod. Mer information om priser finns [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Nästa steg

- [Designmönster för tabellen](table-storage-design-patterns.md)
- [Modeling relationer](table-storage-design-modeling.md)
- [Design för frågor](table-storage-design-for-query.md)
- [Kryptera tabelldata](table-storage-design-encrypt-data.md)
- [Design för dataändring](table-storage-design-for-modification.md)
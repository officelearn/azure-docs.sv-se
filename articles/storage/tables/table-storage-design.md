---
title: Utforma skalbara och högpresterande tabeller i Azure-tabelllagring. | Microsoft Docs
description: Utforma skalbara och högpresterande tabeller i Azure-tabelllagring.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 03/09/2020
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 8df639eea757c374554fa19e57c43cef79308e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255150"
---
# <a name="design-scalable-and-performant-tables"></a>Utforma skalbara och högpresterande tabeller

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Om du vill utforma skalbara och högpresterande tabeller måste du ta hänsyn till faktorer som prestanda, skalbarhet och kostnad. Om du tidigare har utformat scheman för relationsdatabaser är dessa överväganden välbekanta, men även om det finns vissa likheter mellan Azure Table-tjänstlagringsmodellen och relationsmodeller, finns det också viktiga skillnader. Dessa skillnader leder vanligtvis till olika designer som kan se kontraintuitiva eller fel för någon som känner till relationsdatabaser, men ändå är meningsfulla om du utformar för ett NoSQL-nyckel/värdearkiv, till exempel Azure Table-tjänsten. Många av dina designskillnader återspeglar det faktum att tabelltjänsten är utformad för att stödja molnskalaprogram som kan innehålla miljarder entiteter (eller rader i relationsdatabasterminologi) av data eller för datauppsättningar som måste stödja hög transaktion Volymer. Därför måste du tänka annorlunda på hur du lagrar dina data och förstå hur tabelltjänsten fungerar. Ett väl utformat NoSQL-datalager kan göra det möjligt för din lösning att skalas mycket längre och till en lägre kostnad än en lösning som använder en relationsdatabas. Den här guiden hjälper dig med dessa ämnen.  

## <a name="about-the-azure-table-service"></a>Om Azure Table-tjänsten
I det här avsnittet beskrivs några av de viktigaste funktionerna i tabelltjänsten som är särskilt relevanta för att utforma för prestanda och skalbarhet. Om du inte har använt Azure Storage och table-tjänsten tidigare kan du först läsa [Introduktion till Microsoft Azure Storage](../../storage/common/storage-introduction.md) och kom igång med Azure Table Storage med [.NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) innan du läser resten av den här artikeln. Även om fokus för den här guiden ligger på tabelltjänsten, innehåller den en diskussion om Azure Queue- och Blob-tjänsterna och hur du kan använda dem med tabelltjänsten.  

Vad är table-tjänsten? Som du kan förvänta dig av namnet använder tabelltjänsten ett tabellformat för att lagra data. I standardterminologin representerar varje rad i tabellen en entitet och kolumnerna lagrar de olika egenskaperna för den entiteten. Varje entitet har ett par nycklar för att unikt identifiera den och en tidsstämpelkolumn som tabelltjänsten använder för att spåra när entiteten senast uppdaterades. Tidsstämpeln tillämpas automatiskt och du kan inte skriva över tidsstämpeln manuellt med ett godtyckligt värde. Tabelltjänsten använder den här senast ändrade tidsstämpeln (LMT) för att hantera optimistisk samtidighet.  

> [!NOTE]
> REST API-åtgärder för tabelltjänsten returnerar också ett **ETag-värde** som det härleder från LMT. I det här dokumentet används termerna ETag och LMT omväxlande eftersom de refererar till samma underliggande data.  
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


Hittills har dessa data liknar en tabell i en relationsdatabas med de viktigaste skillnaderna är de obligatoriska kolumnerna och möjligheten att lagra flera entitetstyper i samma tabell. Var och en av de användardefinierade egenskaperna, till exempel **Förnamn** eller **Ålder,** har också en datatyp, till exempel heltal eller sträng, precis som en kolumn i en relationsdatabas. Även om tabelltjänstens schemalösa karaktär till skillnad från i en relationsdatabas innebär att en egenskap inte behöver ha samma datatyp på varje entitet. Om du vill lagra komplexa datatyper i en enda egenskap måste du använda ett serialiserat format som JSON eller XML. Mer information om tabelltjänsten, till exempel datatyper som stöds, datumintervall som stöds, namngivningsregler och storleksbegränsningar, finns [i Förstå tabelltjänstdatamodellen](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Ditt val av **PartitionKey** och **RowKey** är grundläggande för bra tabelldesign. Varje entitet som lagras i en tabell måste ha en unik kombination av **PartitionKey** och **RowKey**. Precis som med nycklar i en relationsdatabastabell indexeras **partitionsnyckel-** och **RowKey-värdena** för att skapa ett klustrade index för att möjliggöra snabba uppslag. Tabelltjänsten skapar dock inga sekundära index, så **PartitionKey** och **RowKey** är de enda indexerade egenskaperna. Några av de mönster som beskrivs i [Tabelldesignmönster](table-storage-design-patterns.md) illustrerar hur du kan komma runt den här uppenbara begränsningen.  

En tabell består av en eller flera partitioner, och många av de designbeslut du fattar kommer att vara runt att välja en lämplig **PartitionKey** och **RowKey** för att optimera din lösning. En lösning kan bestå av en enda tabell som innehåller alla dina entiteter ordnade i partitioner, men vanligtvis har en lösning flera tabeller. Tabeller hjälper dig att logiskt organisera dina entiteter, hjälpa dig att hantera åtkomst till data med hjälp av åtkomstkontrollistor och du kan släppa en hel tabell med en enda lagringsåtgärd.  

## <a name="table-partitions"></a>Tabellpartitioner
Kontonamnet, tabellnamnet och **PartitionKey** identifierar tillsammans partitionen i lagringstjänsten där tabelltjänsten lagrar entiteten. Förutom att vara en del av adresssystemet för entiteter definierar partitioner ett scope för transaktioner (se Transaktioner för [entitetsgrupp](#entity-group-transactions) nedan) och utgör grunden för hur tabelltjänsten skalas. Mer information om partitioner finns i [Checklista för prestanda och skalbarhet för tabelllagring](storage-performance-checklist.md).  

I tabelltjänsten tjänsterar en enskild nod en eller flera fullständiga partitioner och tjänsten skalas av dynamiskt belastningsutjämningspartitioner över noder. Om en nod är under belastning kan tabelltjänsten *dela upp* intervallet för partitioner som betjänas av den noden på olika noder. När trafiken avtar kan tjänsten *sammanfoga* partitionsintervallen från tysta noder tillbaka till en enda nod.  

Mer information om den interna informationen om tabelltjänsten, och i synnerhet hur tjänsten hanterar partitioner, finns i dokumentet [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

## <a name="entity-group-transactions"></a>Transaktioner i entitetsgrupp
I tabelltjänsten är entitetsgrupptransaktioner (EGT) den enda inbyggda mekanismen för att utföra atomuppdateringar över flera entiteter. EGTs kallas ibland också *batchtransaktioner.* EGTs kan bara fungera på enheter som lagras i samma partition (det vill ha samma partitionsnyckel i en viss tabell). Så när du behöver atomisk transaktionsbeteende över flera entiteter, måste du se till att dessa entiteter är i samma partition. Detta är ofta en anledning till att behålla flera entitetstyper i samma tabell (och partition) och inte använda flera tabeller för olika entitetstyper. En enda egt kan fungera på högst 100 enheter.  Om du skickar in flera samtidiga egts för bearbetning är det viktigt att se till att dessa egts inte fungerar på enheter som är vanliga över egts; Annars kan behandlingen fördröjas.

EGTs introducerar också en potentiell kompromiss för dig att utvärdera i din design. Det vill än, med hjälp av fler partitioner ökar skalbarheten för ditt program, eftersom Azure har fler möjligheter till belastningsutjämningsbegäranden över noder. Men om du använder fler partitioner kan det begränsa möjligheten för ditt program att utföra atomtransaktioner och upprätthålla en stark konsekvens för dina data. Dessutom finns det specifika skalbarhetsmål på nivån för en partition som kan begränsa dataflödet för transaktioner som du kan förvänta dig för en enda nod. Mer information om skalbarhetsmål för Azure-standardlagringskonton finns i [Skalbarhetsmål för standardlagringskonton](../common/scalability-targets-standard-account.md). Mer information om skalbarhetsmål för tabelltjänsten finns i [Skalbarhets- och prestandamål för tabelllagring](scalability-targets.md).

## <a name="capacity-considerations"></a>Överväganden för kapaciteter

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="cost-considerations"></a>Kostnadsöverväganden
Tabelllagring är relativt billigt, men du bör inkludera kostnadsuppskattningar för både kapacitetsanvändning och antalet transaktioner som en del av utvärderingen av en tabelltjänstlösning. I många fall är det dock en giltig metod att lagra denormaliserade eller duplicerade data för att förbättra lösningens prestanda eller skalbarhet. Mer information om priser finns i [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Nästa steg

- [Mönster för tabelldesign](table-storage-design-patterns.md)
- [Modellera relationer](table-storage-design-modeling.md)
- [Utforma för frågor](table-storage-design-for-query.md)
- [Kryptera tabelldata](table-storage-design-encrypt-data.md)
- [Utforma för dataändring](table-storage-design-for-modification.md)

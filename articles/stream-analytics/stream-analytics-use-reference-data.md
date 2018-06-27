---
title: Använda referensdata för sökningar i Azure Stream Analytics
description: Den här artikeln beskriver hur du använder referensdata att söka efter eller korrelera data i en Azure Stream Analytics-jobbet frågans design.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: 25c25a58b4c6eab5419f645e8e916e034e7803dd
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016898"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Använda referensdata för sökningar i Stream Analytics
Referensdata (även kallat en uppslagstabell) är en begränsad mängd data som är statiska eller långsamt ändra karaktär används för att utföra en sökning eller att korrelera med din dataström. Azure Stream Analytics läser in referensdata i minnet för att uppnå låg latens dataströmmen bearbetning. För att använda referensdata i Azure Stream Analytics-jobbet, använder du normalt en [referens Data ansluta](https://msdn.microsoft.com/library/azure/dn949258.aspx) i frågan. Stream Analytics använder Azure Blob storage som lagringsskikt för referensdata med Azure Data Factory-referensen kan data omvandlas eller kopieras till Azure Blob storage för användning som referensdata från [valfritt antal molnbaserade och lokalt datalager](../data-factory/copy-activity-overview.md). Referensdata modelleras som en serie blobbar (definieras i den inkommande configuration) i stigande ordning efter datum/tid som anges i blob-namn. Den **endast** har stöd för att lägga till i slutet av sekvensen med hjälp av ett datum/tid **större** än den som angetts av den senaste blobben i sekvensen.

Stream Analytics stöder referensdata med **maximal storlek på 300 MB**. 300 MB-gränsen för maximal storlek för referensdata är hastigheterna bara med enkla frågor. När frågan komplexitet ökar för att inkludera tillståndskänslig bearbetning, till exempel fönsteraggregeringar, temporala kopplingar och temporal analysfunktioner förväntas att största tillåtna storleken för referens data minskas. Om Azure Stream Analytics inte kan läsa in referensdata och utföra avancerade åtgärder, kommer jobbet slut på minne och misslyckas. I sådana fall når SU % användning mått 100%.    

|**Antal enheter för strömning**  |**Uppskattat största storlek som stöds (i MB)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 och senare   |300   |

Öka antalet enheter för strömning för ett jobb utöver 6 ökar inte den maximala storleken som stöds för referensdata.

Det finns inte stöd för komprimering för referensdata. 

## <a name="configuring-reference-data"></a>Konfigurera referensdata
Om du vill konfigurera din referensdata måste du först skapa indata som är av typen **referensdata**. Tabellen nedan beskriver varje egenskap måste du ange när du skapar referens indata med beskrivningen:

|**Egenskapsnamn**  |**Beskrivning**  |
|---------|---------|
|Ett inmatat Alias   | Ett eget namn som ska användas i jobb frågan för att referera till den här indata.   |
|Lagringskonto   | Namnet på det lagringskonto där dina blobbar finns. Om den är i samma prenumeration som Stream Analytics-jobbet måste välja du den från listrutan.   |
|Lagringskontonyckel   | Den hemliga nyckeln som associeras med lagringskontot. Detta hämtar fylls automatiskt i om lagringskontot tillhör samma prenumeration som Stream Analytics-jobbet.   |
|Lagringsbehållaren   | Behållare innehåller en logisk gruppering för blobbar som lagras i Microsoft Azure Blob-tjänsten. När du överför en blobb till Blob-tjänsten måste du ange en behållare för blobben.   |
|Sökvägar   | Sökvägen som används för att hitta dina blobbar i den angivna behållaren. I sökvägen, kan du välja att ange en eller flera instanser av följande 2 variabler:<BR>{date}, {time}<BR>Exempel 1: products/{date}/{time}/product-list.csv<BR>Exempel 2: products/{date}/product-list.csv   |
|[Valfritt] datumformat   | Om du har använt {date} i sökvägar som du angav, kan du välja datumformat där dina blobbar ordnas listrutan av de format som stöds.<BR>Exempel: ÅÅÅÅ-MM/DD, åååå-MM-DD osv.   |
|[Valfritt] tidsformat   | Om du har använt {time} i sökvägar som du angav, kan du välja tidsformatet där dina blobbar ordnas listrutan av de format som stöds.<BR>Exempel: HH, HH/mm eller HH-mm.  |
|Händelsen serialiseringsformat   | För att vara säker på att dina frågor fungerar som du vill, behöver Stream Analytics veta vilket serialiseringsformat du använder för inkommande dataströmmar. För referens är format som stöds csv- och JSON.  |
|Kodning   | UTF-8 är det enda kodformat som stöds för närvarande.  |

## <a name="generating-reference-data-on-a-schedule"></a>Generera referensdata enligt ett schema
Om din referensdata är en långsamt föränderliga datauppsättning, sedan stöd för att uppdatera referens data aktiveras genom att ange en sökväg mönster i inkommande konfigurationen med {date} och {time} ersättning token. Stream Analytics hämtar uppdaterade referensdata datadefinitioner baserat på den här sökvägen. Till exempel ett mönster för `sample/{date}/{time}/products.csv` med datumformatet **”åååå-MM-DD”** och ett tidsformat för **”HH-mm”** instruerar Stream Analytics för att hämta uppdaterade blob `sample/2015-04-16/17-30/products.csv` kl: 30 på 16 April , 2015 UTC-tidszonen.

> [!NOTE]
> För närvarande ser Stream Analytics-jobb ut för blob-uppdatering endast när datortiden avancerar till den tid som kodats i blob-namnet. Till exempel jobbet söker efter `sample/2015-04-16/17-30/products.csv` så snart som möjligt, men ingen tidigare än 5:30 PM 16 April 2015 UTC tid zonen. Kommer det att *aldrig* leta efter en blob med ett kodat tidigare än den sista som har identifierats.
> 
> T.ex. när jobbet hittar blob `sample/2015-04-16/17-30/products.csv` kommer att ignorera alla filer som har ett kodat infaller tidigare än 5:30 PM 16 April 2015 så om en sen anländer `sample/2015-04-16/17-25/products.csv` blob skapas i samma behållare jobbet kommer inte att användas.
> 
> Likaså om `sample/2015-04-16/17-30/products.csv` skapas endast klockan 10:03 16 April 2015 men ingen blob med ett tidigare datum finns i behållaren, jobbet kommer att använda den här filen från klockan 10:03 16 April 2015 och använda tidigare referensdata fram till dess.
> 
> Ett undantag startas när jobbet måste nytt bearbeta data i tid eller när jobbet. Vid start söker jobbet efter de senaste blob innan jobbet starttid har angetts. Detta görs för att kontrollera att det finns en **icke-tom** refererar till datauppsättningen när jobbet startas. Om något inte går att hitta jobbet visar följande diagnostik: `Initializing input without a valid reference data blob for UTC time <start time>`.
> 
> 

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) kan användas för att dirigera aktiviteten för att skapa den uppdaterade BLOB-objekt som krävs av Stream Analytics för att uppdatera referens datadefinitioner. Data Factory är en molnbaserad dataintegreringstjänst som samordnar och automatiserar förflyttning och transformering av data. Data Factory stöder [ansluter till ett stort antal moln baserade och lokala datalager](../data-factory/copy-activity-overview.md) och enkelt flytta data enligt ett schema som du anger. Mer information och steg-för-steg-anvisningar om hur du ställer in Data Factory-pipelinen för att generera referensdata för Stream Analytics som uppdateras enligt ett fördefinierat schema kolla detta [GitHub exempel](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## <a name="tips-on-refreshing-your-reference-data"></a>Tips om att uppdatera referensdata
1. Skriver över referens data-blobs inte ska orsaka Stream Analytics att läsa in blob och i vissa fall kan det orsaka jobb misslyckas. Det rekommenderade sättet att ändra referensdata är att lägga till en ny blob med samma behållare och sökvägen mönstret som definieras i jobbet indata och använda en datum/tid **större** än den som angetts av den senaste blobben i sekvensen.
2. Referens för data-blobs är **inte** sorteras i blob ”senaste” modifierades men endast av datum och tid som anges i blob med {date} och {time} ersättningar.
3. Om du vill undvika att behöva listan stort antal blobbar, Överväg att ta bort gamla mycket blobbar som bearbetningen inte längre kommer att ske. Observera att ASA kan gå har Ombearbeta lite i vissa scenarier, t.ex. en omstart.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

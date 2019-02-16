---
title: Använd referensdata för sökningar i Azure Stream Analytics
description: Den här artikeln beskriver hur du använder referensdata för att söka efter eller kombinera data i ett Azure Stream Analytics-jobb frågans design.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: cc8c10f8a3f515d3401dbb469a7e4a31c4fe3501
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329821"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Med hjälp av referensdata för sökningar i Stream Analytics
Referensdata (även kallat en uppslagstabell) är en begränsad mängd data som är statiska eller långsamt ändrad karaktär används för att utföra en sökning eller att korrelera med din dataström. I en IoT-scenario kan du till exempel lagra metadata om sensorer (som inte ändras ofta) i referensdata och träffa realtid IoT-dataströmmar. Azure Stream Analytics läser in referensdata i minnet för att uppnå bearbetning av dataströmmar med låg latens. Att göra använder referensdata i Azure Stream Analytics-jobb kan du vanligtvis använder en [referens Data ansluta](https://msdn.microsoft.com/library/azure/dn949258.aspx) i frågan. 

Stream Analytics stöder Azure Blob storage och Azure SQL Database som storage-skiktet för referensdata. Du kan också transformera och/eller kopiera referensdata till Blob storage från Azure Data Factory för att använda [alla många molnbaserade och lokala datalager](../data-factory/copy-activity-overview.md).

## <a name="azure-blob-storage"></a>Azure Blob Storage

Referensdata modelleras som en serie blobbar (definieras i den inkommande configuration) i stigande ordning efter datum/tid som anges i blobnamnet. Den **endast** har stöd för att lägga till i slutet av sekvensen med hjälp av ett datum/tid **större** än den som angetts av senaste blob i sekvensen.

### <a name="configure-blob-reference-data"></a>Konfigurera blob referensdata

Om du vill konfigurera din referensdata, måste du först skapa en som är av typen **referensdata**. Tabellen nedan beskriver varje egenskap som du måste ange när du skapar referensdata indata med dess beskrivning:

|**Egenskapsnamn**  |**Beskrivning**  |
|---------|---------|
|Indataalias   | Ett eget namn som ska användas i jobbet frågan för att referera till denna indata.   |
|Lagringskonto   | Namnet på lagringskontot där dina blobar finns. Om den finns i samma prenumeration som ditt Stream Analytics-jobb kan du markera den i listrutan.   |
|Lagringskontonyckel   | Den hemliga nyckeln som är associerade med lagringskontot. Det här fylls automatiskt om lagringskontot tillhör samma prenumeration som ditt Stream Analytics-jobb.   |
|Lagringscontainer   | Behållare är en logisk gruppering för blobbar som lagras i Microsoft Azure Blob-tjänsten. När du laddar upp en blob till Blob-tjänsten måste du ange en behållare för blobben.   |
|Sökvägsmönster   | Sökvägen som används för att hitta dina blobbar i den angivna behållaren. I sökvägen, kan du välja att ange en eller flera instanser av följande 2 variabler:<BR>{date}, {time}<BR>Exempel 1: products/{date}/{time}/product-list.csv<BR>Exempel 2: products/{date}/product-list.csv<BR>Exempel 3: product-list.csv<BR><br> Om blobben som inte finns i den angivna sökvägen, väntar Stream Analytics-jobbet på obestämd tid blobben som ska bli tillgänglig.   |
|[Valfritt] datumformat   | Om du har använt {date} i mönstret sökväg som du har angett, kan du välja datumformat där dina blobbar ordnas från listan över de format som stöds.<BR>Exempel: ÅÅÅÅ/MM/DD, MM/DD/ÅÅÅÅ osv.   |
|[Valfritt] tidsformat   | Om du har använt {time} i mönstret sökväg som du har angett, kan du välja tidsformatet där dina blobbar ordnas från listan över de format som stöds.<BR>Exempel: HH, HH/mm eller HH-mm.  |
|Händelseserialiseringsformat   | För att vara säker på att dina frågor fungerar som du vill, behöver Stream Analytics veta vilket serialiseringsformat du använder för inkommande dataströmmar. För referensdata är format som stöds CSV och JSON.  |
|Kodning   | UTF-8 är det enda kodformat som stöds för närvarande.  |

### <a name="static-reference-data"></a>Statiska referensdata

Om din referensdata inte förväntas ändras, sedan stöd för statisk referens för data är aktiverat genom att ange en statisk sökväg i konfigurationen av indata. Azure Stream Analytics hämtar blob från den angivna sökvägen. {date} och {time} ersättningen token inte behövs. Referensdata kan inte ändras i Stream Analytics. Därför kan rekommenderas skriva över en statisk referensdatablob inte.

### <a name="generate-reference-data-on-a-schedule"></a>Generera referensdata enligt ett schema

Om din referensdata är en uppsättning med långsamt föränderliga data, stöd för för att uppdatera referens för data är aktiverat genom att ange en sökvägsmönster i den inkommande konfiguration med hjälp av den {date} och {time} ersättningen token. Stream Analytics hämtar uppdaterade referensdata datadefinitionerna baserat på det här mönstret för sökvägen. Till exempel ett mönster av `sample/{date}/{time}/products.csv` med datumformatet **”åååå-MM-DD”** och ett tidsformat för **”HH-mm”** instruerar Stream Analytics för att hämta den uppdaterade blobben `sample/2015-04-16/17-30/products.csv` 17:30:00 på den 16 April , 2015 UTC-tidszonen.

Azure Stream Analytics söker automatiskt efter uppdateras referensdatablobar med en minuts intervall.

> [!NOTE]
> För närvarande leta Stream Analytics-jobb efter den blob-uppdateringen bara när datortiden nyheterna till den tidpunkt som kodats i blobnamnet. Till exempel jobbet söker efter `sample/2015-04-16/17-30/products.csv` så snart som möjligt men inte tidigare än 17:30:00 den 16 April 2015 UTC tidszonen. Kommer det att *aldrig* leta efter en blob med en kodad tid tidigare än det senaste som har identifierats.
> 
> T.ex. när jobbet hittar blob `sample/2015-04-16/17-30/products.csv` kommer att ignorera filer med en kodad datum tidigare än 17:30:00 den 16 April 2015 så om en sent inkommer `sample/2015-04-16/17-25/products.csv` blob skapas i samma behållare jobbet kommer inte att användas.
> 
> På samma sätt om `sample/2015-04-16/17-30/products.csv` produceras bara klockan 10:03 16 April 2015, men inga blob med ett tidigare datum finns i behållaren, jobbet ska använda den här filen startar klockan 10:03 16 April 2015 och använda tidigare referensdata fram till dess.
> 
> Ett undantag startas när jobbet behöver igen bearbeta data bakåt i tiden eller när jobbet. Jobbet är ute efter senaste blob skapas innan jobbet börjar tid som anges vid start. Detta görs för att kontrollera att det finns en **icke-tomma** referensdatauppsättning när jobbet börjar. Om en inte kan hittas, visar följande diagnostik: `Initializing input without a valid reference data blob for UTC time <start time>`.

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) kan användas för att dirigera uppgiften med att skapa de uppdaterade blobbar som krävs av Stream Analytics för att uppdatera definitioner för referens för data. Data Factory är en molnbaserad dataintegreringstjänst som samordnar och automatiserar förflyttning och transformering av data. Data Factory stöder [ansluter till ett stort antal moln baserade och lokala datalager](../data-factory/copy-activity-overview.md) och flytta data enkelt enligt ett schema som du anger. Mer information och stegvisa anvisningar för hur du ställer in en Data Factory-pipeline för att generera referensdata för Stream Analytics som uppdateras enligt ett fördefinierat schema kan du titta i den här [GitHub-exempel](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

### <a name="tips-on-refreshing-blob-reference-data"></a>Tips om hur du uppdaterar data i blob-referens

1. Skriv inte över referensdatablobar eftersom de inte kan ändras.
2. Det rekommenderade sättet att uppdatera referensdata är att:
    * Använd {date} / {time} i sökväg-mönster
    * Lägg till en ny blob med hjälp av samma behållare och sökvägen mönstret som definierats i jobbet indata
    * Använd ett datum/tid **större** än den som angetts av senaste blob i sekvensen.
3. Referensdatablobar är **inte** sorteras efter blobens ”senast ändrad” tid men endast av tid och datum som anges i blob namn med hjälp av den {date} och {time} ersättning.
3. Om du vill undvika att behöva lista stort antal blobbar, Överväg att ta bort mycket gamla blobbar som bearbetningen inte längre kommer att ske. Observera att ASA försätts har ombearbetning av en viss i vissa scenarier som en omstart.

## <a name="azure-sql-database-preview"></a>Azure SQL-databas (förhandsversion)

Azure SQL Database-referensdata hämtas av ditt Stream Analytics-jobb och lagras som en ögonblicksbild i minnet för bearbetning. Ögonblicksbild av din referensdata lagras också i en behållare i ett lagringskonto som du anger i konfigurationsinställningarna. Behållaren har skapats automatiskt när jobbet börjar. Om jobbet har stoppats eller försätts i ett felaktigt tillstånd, tas de automatisk skapade behållarna bort när jobbet startas om.  

Om din referensdata är en uppsättning med långsamt föränderliga data, måste regelbundet uppdatera ögonblicksbilden som används i jobbet. Stream Analytics kan du ange ett intervall när du konfigurerar din Azure SQL Database inkommande anslutning. Stream Analytics-runtime frågar Azure SQL Database med det intervall som anges av uppdateringsfrekvensen. Den snabbaste uppdateringsintervall som stöds är en gång per minut. Stream Analytics lagrar en ny ögonblicksbild i storage-kontot som angetts för varje uppdatering.

Stream Analytics har två alternativ för att fråga Azure SQL Database. En ögonblicksbild fråga är obligatoriskt och måste inkluderas i varje jobb. Stream Analytics kör ögonblicksbild-frågan med jämna mellanrum baserat på din uppdateringsintervall och använder resultatet av frågan (snapshot) som referensdatauppsättningen. Ögonblicksbild frågan ska rymmas i de flesta fall, men om du får prestandaproblem med stora datamängder och snabb uppdateringsintervall, du kan använda frågealternativet delta.

Med frågealternativet delta kör Stream Analytics ögonblicksbild-fråga från början för att få en referensdatauppsättning för baslinjen. Efter kör Stream Analytics deltafråga med jämna mellanrum baserat på din uppdateringsintervallet för att hämta stegvisa ändringar. Dessa stegvisa ändringar tillämpas kontinuerligt referensdatauppsättning att hålla det uppdaterat. Deltafråga hjälp kan minska kostnaden för lagring och nätverks-i/o-åtgärder.

### <a name="configure-sql-database-reference"></a>Konfigurera SQL Database-referens

Om du vill konfigurera din referensdata för SQL-databas, måste du först skapa **referensdata** indata. Tabellen nedan beskriver varje egenskap som du måste ange när du skapar referensdata indata med en beskrivning. Mer information finns i [Använd referensdata från en SQL-databas till Azure Stream Analytics-jobb](sql-reference-data.md).

|**Egenskapsnamn**|**Beskrivning**  |
|---------|---------|
|Inmatat alias|Ett eget namn som ska användas i jobbet frågan för att referera till denna indata.|
|Prenumeration|Välj din prenumeration|
|Databas|Azure SQL-databasen som innehåller din referensdata.|
|Användarnamn|Användarnamnet som är associerade med din Azure SQL Database.|
|Lösenord|Lösenordet som associeras med din Azure SQL Database.|
|Uppdatera regelbundet|Det här alternativet kan du välja ett intervall. Välja ”On” kan du ange uppdateringsfrekvensen i DD:HH:MM.|
|Ögonblicksbildsfråga|Det här är standardalternativet för frågan som hämtar referensdata från din SQL-databas.|
|Deltafråga|Uppdateringsintervall, Välj att lägga till en deltafråga för avancerade scenarier med stora datauppsättningar och en kort.|

## <a name="size-limitation"></a>Storleksgräns

Stream Analytics stöder referensdata med **maximal storlek på 300 MB**. 300 MB-gränsen för maximal storlek på referensdata är kan uppnås endast med enkla frågor. När frågan komplexitet ökar för att inkludera tillståndskänsliga bearbetningskällor, exempelvis fönsteraggregeringar, temporala kopplingar och temporala analysfunktioner förväntas att största storlek för referens data minskar som stöds. Om Azure Stream Analytics inte kan läsa in referensdata och utföra avancerade åtgärder, kommer jobbet slut på minne och misslyckas. I sådana fall kan når SU % utnyttjande mått 100%.    

|**Antal enheter för strömning**  |**CA maxstorlek som stöds (i MB)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 och senare   |300   |

Öka antalet enheter för strömning för ett jobb utöver 6 ökar inte den maximala storleken som stöds för referensdata.

Stöd för komprimering är inte tillgängligt för referensdata. 

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

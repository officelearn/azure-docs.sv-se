---
title: Använda referensdata för uppslag i Azure Stream Analytics
description: I den här artikeln beskrivs hur du använder referensdata för att slå upp eller korrelera data i ett Azure Stream Analytics-jobbs frågedesign.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: b3808524706b13761dd8eccffa301c602d08f481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267292"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Använda referensdata för uppslag i Stream Analytics

Referensdata (kallas även en uppslagstabell) är en begränsad datauppsättning som är statisk eller långsamt föränderlig i naturen, som används för att utföra en sökning eller för att utöka dina dataströmmar. I ett IoT-scenario kan du till exempel lagra metadata om sensorer (som inte ändras ofta) i referensdata och ansluta till dem med IoT-dataströmmar i realtid. Azure Stream Analytics läser in referensdata i minnet för att uppnå bearbetning av låg latensström. Om du vill använda referensdata i ditt Azure Stream Analytics-jobb använder du vanligtvis en [referensdataanslutning](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) i din fråga. 

Stream Analytics stöder Azure Blob storage och Azure SQL Database som lagringslagret för referensdata. Du kan också omvandla och/eller kopiera referensdata till Blob-lagring från Azure Data Factory om du vill använda [valfritt antal molnbaserade och lokala datalager](../data-factory/copy-activity-overview.md).

## <a name="azure-blob-storage"></a>Azure Blob Storage

Referensdata modelleras som en sekvens av blobbar (definierade i indatakonfigurationen) i stigande ordning för det datum/den tid som anges i blobnamnet. Den stöder **bara** att lägga till i slutet av sekvensen genom att använda ett datum/en tid **som är större** än det som angavs av den sista blobben i sekvensen.

### <a name="configure-blob-reference-data"></a>Konfigurera referensdata för blob

Om du vill konfigurera referensdata måste du först skapa en indata som är av typen **Referensdata**. I tabellen nedan förklaras varje egenskap som du måste ange när du skapar referensdatainmatningen med dess beskrivning:

|**Egenskapsnamn**  |**Beskrivning**  |
|---------|---------|
|Indataalias   | Ett eget namn som ska användas i jobbfrågan för att referera till indata.   |
|Lagringskonto   | Namnet på lagringskontot där blobbar finns. Om den finns i samma prenumeration som ditt Stream Analytics-jobb kan du välja det i listrutan.   |
|Nyckeln för lagringskonto   | Den hemliga nyckeln som är associerad med lagringskontot. Detta fylls i automatiskt om lagringskontot finns i samma prenumeration som ditt Stream Analytics-jobb.   |
|Behållare för lagring   | Behållare ger en logisk gruppering för blobbar som lagras i Microsoft Azure Blob-tjänsten. När du överför en blob till Blob-tjänsten måste du ange en behållare för den blobben.   |
|Sökvägsmönster   | Sökvägen som används för att hitta blobbar i den angivna behållaren. I sökvägen kan du välja att ange en eller flera instanser av följande två variabler:<BR>{datum}, {tid}<BR>Exempel 1: produkter/{date}/{time}/product-list.csv<BR>Exempel 2: produkter/{date}/product-list.csv<BR>Exempel 3: product-list.csv<BR><br> Om blobben inte finns i den angivna sökvägen väntar Stream Analytics-jobbet på obestämd tid för att blobben ska bli tillgänglig.   |
|Datumformat [valfritt]   | Om du har använt {date} i det angivna banmönstret kan du välja det datumformat där blobbar är ordnade från listrutan format som stöds.<BR>Exempel: YYYY/MM/DD, MM/DD/YYYY, etc.   |
|Tidsformat [valfritt]   | Om du har använt {time} i det banmönster som du angav kan du välja det tidsformat som blobbar är ordnade i listrutan för format som stöds.<BR>Exempel: HH, HH/mm eller HH-mm.  |
|Format för seriell händelse   | Om du vill vara säker på att dina frågor fungerar som förväntat måste Stream Analytics veta vilket serialiseringsformat du använder för inkommande dataströmmar. För referensdata är de format som stöds CSV och JSON.  |
|Kodning   | UTF-8 är det enda kodningsformat som stöds just nu.  |

### <a name="static-reference-data"></a>Statiska referensdata

Om referensdata inte förväntas ändras aktiveras stöd för statiska referensdata genom att ange en statisk sökväg i indatakonfigurationen. Azure Stream Analytics hämtar blobben från den angivna sökvägen. {date} och {time} ersättningstoken krävs inte. Eftersom referensdata är oföränderliga i Stream Analytics rekommenderas inte att skriva över en statisk referensdatablob.

### <a name="generate-reference-data-on-a-schedule"></a>Generera referensdata enligt ett schema

Om referensdata är en långsamt föränderlig datauppsättning aktiveras stöd för uppdatering av referensdata genom att ange ett sökvägsmönster i indatakonfigurationen med hjälp av substitutionstoken {date} och {time}. Stream Analytics hämtar de uppdaterade referensdatadefinitionerna baserat på det här sökvägsmönstret. Ett `sample/{date}/{time}/products.csv` mönster med ett datumformat med **"Å ÅYY-MM-DD"** och ett tidsformat med **"HH-mm"** instruerar `sample/2015-04-16/17-30/products.csv` stream analytics att plocka upp den uppdaterade blobben klockan 17:30 den 16 april 2015 UTC-tidszonen.

Azure Stream Analytics söker automatiskt efter uppdaterade referensdatablobar med ett minutsintervall. Om en blob med tidsstämpel 10:30:00 överförs med en liten fördröjning (till exempel 10:30:30) kommer du att märka en liten fördröjning i Stream Analytics-jobb som refererar till den här bloben. För att undvika sådana scenarier rekommenderar vi att blobben överförs tidigare än målets effektiva tid (10:30:00 i det här exemplet) så att Stream Analytics-jobbet får tillräckligt med tid för att identifiera och läsa in det i minnet och utföra åtgärder. 

> [!NOTE]
> Stream Analytics-jobb söker bara efter blob-uppdatering när datortiden går framåt till den tid som kodas i blobnamnet. Jobbet söker `sample/2015-04-16/17-30/products.csv` till exempel så snart som möjligt, men tidigast 17:30 den 16 april 2015 UTC-tidszon. Det kommer *aldrig* att leta efter en blob med en kodad tid tidigare än den sista som upptäcks.
> 
> När jobbet till exempel hittar `sample/2015-04-16/17-30/products.csv` blobben ignoreras alla filer med ett kodat datum tidigare än 17:30 den 16 `sample/2015-04-16/17-25/products.csv` april 2015 så om en sen ankommande blob skapas i samma behållare kommer jobbet inte att använda det.
> 
> På `sample/2015-04-16/17-30/products.csv` samma sätt om det bara produceras klockan 10:03 16 april 2015 men ingen blob med ett tidigare datum finns i behållaren, kommer jobbet att använda den här filen från 22:03 den 16 april 2015 och använda de tidigare referensdata fram till dess.
> 
> Ett undantag från detta är när jobbet behöver bearbeta data tillbaka i tiden eller när jobbet startas. Vid starttid söker jobbet efter den senaste blob som produceras före den angivna jobbstarttiden. Detta görs för att säkerställa att det finns en **icke-tom** referensdatauppsättning när jobbet startar. Om det inte går att hitta något `Initializing input without a valid reference data blob for UTC time <start time>`visar jobbet följande diagnostik: .

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) kan användas för att dirigera uppgiften att skapa de uppdaterade blobbar som krävs av Stream Analytics för att uppdatera referensdatadefinitioner. Data Factory är en molnbaserad dataintegreringstjänst som samordnar och automatiserar förflyttning och transformering av data. Data Factory stöder [anslutning till ett stort antal molnbaserade och lokala datalager](../data-factory/copy-activity-overview.md) och flyttar data enkelt enligt ett regelbundet schema som du anger. Mer information och steg för steg-vägledning om hur du konfigurerar en Data Factory-pipeline för att generera referensdata för Stream Analytics som uppdateras enligt ett fördefinierat schema finns i det här [GitHub-exemplet](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs).

### <a name="tips-on-refreshing-blob-reference-data"></a>Tips om hur du uppdaterar blobreferensdata

1. Skriv inte över referensdatablobar eftersom de är oföränderliga.
2. Det rekommenderade sättet att uppdatera referensdata är att:
    * Använd {date}/{time} i banmönstret
    * Lägga till en ny blob med samma behållare och banmönster som definierats i jobbindata
    * Använd ett datum/en tid **som är större** än det som angavs av den sista blobben i sekvensen.
3. Referensdatablobar ordnas **inte** av blobens "Senast ändrade" tid utan endast efter den tid och det datum som anges i blobnamnet med hjälp av {date} och {time} substitutioner.
3. Om du vill undvika att behöva lista ett stort antal blobbar bör du överväga att ta bort mycket gamla blobbar för vilka bearbetning inte längre kommer att göras. Observera att ASA kan gå att bearbeta en liten mängd i vissa scenarier som en omstart.

## <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database referensdata hämtas av ditt Stream Analytics-jobb och lagras som en ögonblicksbild i minnet för bearbetning. Ögonblicksbilden av referensdata lagras också i en behållare i ett lagringskonto som du anger i konfigurationsinställningarna. Behållaren skapas automatiskt när jobbet startar. Om jobbet stoppas eller går in i ett misslyckat tillstånd tas de automatiskt skapade behållarna bort när jobbet startas om.  

Om referensdata är en långsamt föränderlig datauppsättning måste du regelbundet uppdatera ögonblicksbilden som används i jobbet. Med Stream Analytics kan du ange en uppdateringsfrekvens när du konfigurerar indataanslutningen för Azure SQL Database. Stream Analytics-körningen kommer att fråga din Azure SQL-databas med det intervall som anges av uppdateringsfrekvensen. Den snabbaste uppdateringsfrekvensen som stöds är en gång per minut. För varje uppdatering lagrar Stream Analytics en ny ögonblicksbild i lagringskontot som tillhandahålls.

Stream Analytics innehåller två alternativ för att fråga din Azure SQL-databas. En ögonblicksbildfråga är obligatorisk och måste inkluderas i varje jobb. Stream Analytics kör ögonblicksbildfrågan regelbundet baserat på uppdateringsintervallet och använder resultatet av frågan (ögonblicksbilden) som referensdatauppsättning. Ögonblicksbildfrågan bör passa de flesta scenarier, men om du stöter på prestandaproblem med stora datauppsättningar och snabba uppdateringsfrekvens kan du använda deltafrågan. Frågor som tar mer än 60 sekunder att returnera referensdatauppsättningen resulterar i en timeout.

Med deltafrågan körs ögonblicksbildfrågan från början för att hämta en referensdatauppsättning för baslinjereferens. Därefter kör Stream Analytics deltafrågan regelbundet baserat på uppdateringsintervallet för att hämta inkrementella ändringar. Dessa inkrementella ändringar tillämpas kontinuerligt på referensdatauppsättningen för att hålla den uppdaterad. Med delta-frågan kan du minska lagringskostnaderna och nätverks-I/O-åtgärder.

### <a name="configure-sql-database-reference"></a>Konfigurera SQL-databasreferens

Om du vill konfigurera referensdata för SQL-databasen måste du först skapa **referensdataindata.** I tabellen nedan förklaras varje egenskap som du måste ange när du skapar referensdatainmatningen med dess beskrivning. Mer information finns i [Använda referensdata från en SQL-databas för ett Azure Stream Analytics-jobb](sql-reference-data.md).

Du kan använda [Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) som referensdataindata. Du måste [konfigurera offentlig slutpunkt i Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) och sedan konfigurera följande inställningar manuellt i Azure Stream Analytics. Azure virtuell dator som kör SQL Server med en bifogad databas stöds också genom att manuellt konfigurera inställningarna nedan.

|**Egenskapsnamn**|**Beskrivning**  |
|---------|---------|
|Inmatat alias|Ett eget namn som ska användas i jobbfrågan för att referera till indata.|
|Prenumeration|Välj din prenumeration|
|Databas|Azure SQL-databasen som innehåller dina referensdata. För Azure SQL Database Managed Instance måste den ange port 3342. Exempel *på exempel, sampleserver.public.database.windows.net,3342*|
|Användarnamn|Användarnamnet som är associerat med din Azure SQL-databas.|
|lösenord|Lösenordet som är associerat med din Azure SQL-databas.|
|Uppdatera regelbundet|Med det här alternativet kan du välja en uppdateringsfrekvens. Om du väljer "På" kan du ange uppdateringsfrekvensen i DD:HH:MM.|
|Fråga om ögonblicksbilder|Det här är standardalternativet för fråga som hämtar referensdata från SQL Database.|
|Delta-fråga|För avancerade scenarier med stora datauppsättningar och en kort uppdateringsfrekvens väljer du att lägga till en deltafråga.|

## <a name="size-limitation"></a>Storleksbegränsning

Stream Analytics stöder referensdata med **en maximal storlek på 300 MB**. Gränsen på 300 MB för maximal storlek på referensdata kan endast uppnås med enkla frågor. När komplexiteten i frågan ökar för att inkludera tillståndskänslig bearbetning, till exempel fönsterade aggregat, temporala kopplingar och temporala analytiska funktioner, förväntas den maximala storleken på referensdata minska. Om Azure Stream Analytics inte kan läsa in referensdata och utföra komplexa åtgärder, kommer jobbet att ta på minne och misslyckas. I sådana fall kommer SU % Utnyttjandemått att nå 100 %.    

|**Antal strömningsenheter**  |**Ungefär maxstorlek som stöds (i MB)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 och därefter   |300   |

Att öka antalet strömningsenheter för ett jobb utöver 6 ökar inte referensdatas maximala storlek som stöds.

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

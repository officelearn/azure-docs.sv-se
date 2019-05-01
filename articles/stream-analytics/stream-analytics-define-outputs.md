---
title: Förstå utdata från Azure Stream Analytics
description: Den här artikeln beskriver data utdata alternativen som finns i Azure Stream Analytics, inklusive Power BI för analysresultat.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/25/2019
ms.custom: seodec18
ms.openlocfilehash: f9663fb502d9eda4810c54a89a94481b7d00cd43
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926065"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Förstå utdata från Azure Stream Analytics
Den här artikeln beskrivs vilka typer av utdata som är tillgängliga för Azure Stream Analytics-jobb. Utdata kan du lagra och spara resultatet av Stream Analytics-jobb. Med hjälp av utdata, kan du göra ytterligare affärsanalys och datalager för dina data.

När du utformar din Stream Analytics-fråga, referera till namnet på utdata genom att använda den [INTO-sats](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics). Du kan använda ett enda utflöde per jobb eller flera utdata per direktuppspelningsjobbet (om du behöver dem) genom att tillhandahålla flera INTO-satser i fråga.

Skapa, redigera och testa Stream Analytics-jobbet matar ut, du kan använda den [Azure-portalen](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output), och [Visual Studio](stream-analytics-quick-create-vs.md).

Vissa typer av stöd för utdata [partitionering](#partitioning). [Utdata batch storlekar](#output-batch-size) variera för att optimera genomflödet.


## <a name="azure-data-lake-store"></a>Azure Data Lake Store
Stream Analytics stöder [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store är en företagsomfattande, lagringsplats i hyperskala för analysarbetsbelastningar med stordata. Du kan använda Data Lake Store för att lagra data i alla storlekar, typer och inmatning hastighet för driftsanalyser och undersökande analyser. Stream Analytics måste ha behörighet att komma åt Data Lake Store.

Azure Data Lake Store-utdata från Stream Analytics är för närvarande inte tillgänglig i regionerna för Azure Tyskland (T-Systems International) och Azure Kina (21Vianet).

### <a name="authorize-an-azure-data-lake-store-account"></a>Auktorisera ett Azure Data Lake Store-konto

1. När du väljer Data Lake Store som utdata i Azure-portalen uppmanas du att auktorisera en anslutning till en befintlig Data Lake Store-instans.

   ![Auktorisera en anslutning till Data Lake Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)

2. Om du redan har åtkomst till Data Lake Store väljer **auktorisera nu**. En sida öppnas och visar statusen **omdirigering till auktorisering**. När auktoriseringen har slutförts visas sidan där du kan konfigurera Data Lake Store-utdata.

3. När du har Data Lake Store-konto som autentiseras, konfigurerar du egenskaperna för Data Lake Store-utdata.

   ![Definiera Data Lake Store som Stream Analytics-utdata](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)

I följande tabell visas egenskapsnamn och deras beskrivningar för att konfigurera din Data Lake Store-utdata.   

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias | Ett eget namn som används i frågor för att dirigera utdata till Data Lake Store. |
| Kontonamn | Namnet på Data Lake Store-konto där du skickar dina utdata. Visas med en nedrullningsbar lista över Data Lake Store-konton som är tillgängliga i din prenumeration. |
| Prefixmönster för sögväg | Filsökvägen som används för att skriva dina filer inom det angivna Data Lake Store-kontot. Du kan ange en eller flera instanser av den {date} och {time} variabler:<br /><ul><li>Exempel 1: mapp1/logs / {date} / {time}</li><li>Exempel 2: mapp1/logs / {date}</li></ul><br />Tidsstämpeln för den skapade mappstrukturen följer UTC-tid och inte lokal tid.<br /><br />Om filen sökvägsmönster inte innehåller ett avslutande snedstreck (/), behandlas senaste mönstret i sökvägen som ett fil-namnprefix. <br /><br />Nya filer skapas i dessa fall:<ul><li>Ändra i utdata-schemat</li><li>Extern eller intern omstart av ett jobb</li></ul> |
| Datumformat | Valfri. Du kan välja datumformat där filerna ordnas om datumtoken används i prefixsökvägen. Exempel: ÅÅÅÅ/MM/DD |
|Tidsformat | Valfri. Om tiden token används i prefixsökvägen, ange tidsformatet där filerna ordnas. Det enda värdet som stöds är för närvarande HH. |
| Händelseserialiseringsformat | Serialiseringsformat för utdata. JSON-, CSV- och Avro stöds.|
| Kodning | Om du använder CSV eller JSON-format, måste en kodning anges. UTF-8 är det enda kodformat som stöds för närvarande.|
| Avgränsare | Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering av CSV-data. Värden som stöds är kommatecken, semikolon, utrymme, fliken och lodrät stapel.|
| Format | Gäller endast för JSON-serialisering. **Radseparerade** anger att utdata formateras genom att låta varje JSON-objekt avgränsas med en ny rad. **Matris** anger att utdata formateras som en matris av JSON-objekt. Den här matrisen stängs först när jobbet stoppas eller Stream Analytics har gått vidare till nästa tidsfönstret. I allmänhet är det bättre att använda rad kommaavgränsade JSON, eftersom det inte krävs någon särskild hantering när utdatafilen skrivs fortfarande till.|

### <a name="renew-data-lake-store-authorization"></a>Förnya auktoriseringen för Data Lake Store
Du måste autentiseras på nytt Data Lake Store-konto om lösenordet har ändrats sedan jobbet skapades eller senast autentiserade. Om du inte återautentisera jobbet producerar inte utdata resultatet och visar ett fel som anger behovet av omauktorisering i loggarna för åtgärden. 

För närvarande behöver autentiseringstoken uppdateras manuellt efter 90 dagar för alla jobb med Data Lake Store-utdata. Du kan lösa den här begränsningen av [autentiserar med hjälp av hanterade identiteter (förhandsversion)](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-managed-identities-adls).

Förnya auktorisering:

1. Välj **stoppa** att stoppa jobbet.
1. Gå till ditt Data Lake Store-utdata och välj den **förnya auktoriseringen** länk.

   Under en kort tid, ett popup-sidan anger **omdirigering till auktorisering**. Om auktoriseringen är klar, sidan anger **auktoriseringen har förnyats** och stänger sedan automatiskt. 
   
1. Välj **spara** längst ned på sidan. Du kan sedan starta om jobbet från den **senast stoppad** att undvika dataförlust.

![Förnya auktoriseringen för Data Lake Store i utdata](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)

## <a name="sql-database"></a>SQL Database
Du kan använda [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) som utdata för data som är relationell natur och för program som är beroende av innehållet finns i en relationsdatabas. Stream Analytics-jobb att skriva till en befintlig tabell i SQL-databas. Tabellschemat måste exakt matcha fälten och deras typer i jobbets utdata. Du kan också ange [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) utdataalternativet som utdata via SQL-databasen. Läs mer om sätt att förbättra genomströmning för skrivning, i den [Stream Analytics med Azure SQL Database som utdata](stream-analytics-sql-output-perf.md) artikeln. 

I följande tabell visas egenskapsnamn och deras beskrivning för att skapa en SQL Database-utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera utdata till den här databasen. |
| Databas | Namnet på databasen där du skickar dina utdata. |
| servernamn | SQL Database-servernamn. |
| Användarnamn | Det användarnamn som har skrivbehörighet till databasen. Stream Analytics stöder endast SQL-autentisering. |
| Lösenord | Lösenordet för att ansluta till databasen. |
| Tabell | Tabellnamnet där skrivs utdata. Tabellnamnet är skiftlägeskänsligt. Schemat för den här tabellen måste exakt matcha antalet fält och deras typer som din jobbutdata genererar. |
|Ärv partitionsschema| Ett alternativ för arv partitioneringsschemat av din föregående fråga steg att aktivera fullständigt parallella topologi med flera skrivare i tabellen. Mer information finns i [Azure Stream Analytics-utdata till Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Matcha batchantal| Rekommenderade gränsen för antalet poster som skickas med varje bulk insert transaktion.|

> [!NOTE]
> Azure SQL Database-erbjudande är för närvarande en jobbutdata i Stream Analytics. Azure-datorer som kör SQL Server med en-databas ansluten stöds inte. Detta kan ändras i framtida versioner.
>

## <a name="blob-storage"></a>Blob Storage
Azure Blob storage erbjuder en kostnadseffektiv och skalbar lösning för att lagra stora mängder Ostrukturerade data i molnet. En introduktion på Blob storage och dess användning i [hur du använder blobar](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

I följande tabell visas vilka egenskapsnamn och deras beskrivningar för att skapa en blob-utdata.

| Egenskapsnamn       | Beskrivning                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Utdataalias        | Ett eget namn som används i frågor för att dirigera utdata till blob storage. |
| Lagringskonto     | Namnet på det lagringskonto där du skickar dina utdata.               |
| Lagringskontonyckel | Den hemliga nyckeln som är associerade med lagringskontot.                              |
| Lagringscontainrar   | En logisk gruppering för blobbar som lagras i Azure Blob-tjänsten. När du laddar upp en blob till Blob-tjänsten måste du ange en behållare för blobben. |
| Sökvägsmönster | Valfri. Mönster för fil-sökvägen som används för att skriva dina blobbar i den angivna behållaren. <br /><br /> I mönstret sökvägen kan du använda en eller flera instanser av variabler datum och tid för att ange hur ofta som BLOB-objekt skrivs: <br /> {date}, {time} <br /><br />Du kan använda anpassade blob partitionering för att ange en anpassad {fältet}-namnet från dina händelsedata till BLOB-partitionen. Fältnamnet är alfanumeriska och kan innehålla blanksteg, bindestreck och understreck. Följande: begränsningar för anpassade fält <ul><li>Fältnamn är inte skiftlägeskänsliga. Till exempel kan inte tjänsten skilja mellan kolumnen ”ID” och kolumnen ”id”.</li><li>Kapslade fält tillåts inte. Använd istället ett alias i jobbet frågan ”platta ut” fältet.</li><li>Uttryck kan inte användas som ett fältnamn.</li></ul> <br />Den här funktionen kan du använda anpassade datum/tid-format specificerare konfigurationer i sökvägen. Anpassat datum och tid format måste vara angivna en i taget, omgivna av den {datetime:\<specificerare >} nyckelord. Tillåten indata för \<specificerare > är åååå, MM, M, dd, d, HH, H, mm, m, ss eller s. De {datetime:\<specificerare >} nyckelord kan användas flera gånger i sökvägen för att skapa anpassade datum/tid-konfigurationer. <br /><br />Exempel: <ul><li>Exempel 1: cluster1/logs / {date} / {time}</li><li>Exempel 2: cluster1/logs / {date}</li><li>Exempel 3: cluster1 / {client_id} / {date} / {time}</li><li>Exempel 4: cluster1 / {datetime:ss} / {myField} där frågan är: Välj data.myField som myField från indata;</li><li>Exempel 5: cluster1/år = {datetime:yyyy} / månad = {datetime:MM} per dag = {datetime:dd}</ul><br />Tidsstämpeln för den skapade mappstrukturen följer UTC-tid och inte lokal tid.<br /><br />Filnamngivning använder enligt följande konvention: <br /><br />{Path Prefix Pattern}/schemaHashcode_Guid_Number.extension<br /><br />Exempel utdatafilerna:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Mer information om den här funktionen finns i [Azure Stream Analytics anpassade blob-utdata partitionering](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Datumformat | Valfri. Du kan välja datumformat där filerna ordnas om datumtoken används i prefixsökvägen. Exempel: ÅÅÅÅ/MM/DD |
| Tidsformat | Valfri. Om tiden token används i prefixsökvägen, ange tidsformatet där filerna ordnas. Det enda värdet som stöds är för närvarande HH. |
| Händelseserialiseringsformat | Serialiseringsformat för utdata. JSON-, CSV- och Avro stöds. |
| Kodning    | Om du använder CSV eller JSON-format, måste en kodning anges. UTF-8 är det enda kodformat som stöds för närvarande. |
| Avgränsare   | Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering av CSV-data. Värden som stöds är kommatecken, semikolon, utrymme, fliken och lodrät stapel. |
| Format      | Gäller endast för JSON-serialisering. **Radseparerade** anger att utdata formateras genom att låta varje JSON-objekt avgränsas med en ny rad. **Matris** anger att utdata formateras som en matris av JSON-objekt. Den här matrisen stängs först när jobbet stoppas eller Stream Analytics har gått vidare till nästa tidsfönstret. I allmänhet är det bättre att använda rad kommaavgränsade JSON, eftersom det inte krävs någon särskild hantering när utdatafilen skrivs fortfarande till. |

När du använder Blob storage som utdata, skapas en ny fil i blob i följande fall:

* Om filen överskrider det maximala antalet tillåtna block (för närvarande 50 000). Du kan nå det maximala antalet tillåtna block utan att nå den högsta tillåtna blobstorleken. Till exempel om frekvensen av utgående är hög, du kan se mer byte per block och filstorleken är större. Om frekvensen av utgående är låg, varje block har mindre data och filstorleken är mindre.
* Om det finns en schemaändring i utdata och utdataformat kräver fast schema (CSV och Avro).
* Om ett jobb startas antingen externt av en användare stoppa den och sedan starta den eller internt för återställning av underhåll eller fel.
* Om frågan är partitionerad fullständigt och en skapas ny fil för varje utdata partition.
* Om användaren tar bort en fil eller en behållare för lagringskontot.
* Om utdata är tid som har partitionerats med hjälp av prefixmönster för sögväg och en ny blob som ska användas när frågan flyttar till nästa timma.
* Om utdata har partitionerats med ett anpassat fält och en ny blob skapas per partition nyckel om finns den inte.
* Om utdata har partitionerats med ett anpassat fält där partitionen nyckeln kardinalitet överskrider 8 000 och en ny blob skapas per partitionsnyckel.

## <a name="event-hubs"></a>Event Hubs
Den [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) service är en mycket skalbar lösning för publicering-prenumeration händelse. Det kan samla in miljontals händelser per sekund. En användning av en event hub som utdata är när resultatet av ett Stream Analytics-jobb blir indata för en annan strömningsuppgift.

Du behöver några parametrar för att konfigurera dataströmmar från event hubs som utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias | Ett eget namn som används i frågor för att dirigera utdata till den här händelsehubben. |
| Namnområde för händelsehubb |En behållare för en uppsättning meddelandeentiteter. När du har skapat en ny händelsehubb, skapade du även ett händelsehubbnamnområde. |
| Namn på händelsehubb | Namnet på din event hub-utdata. |
| Principnamn för Event hub | Princip för delad åtkomst som du kan skapa i event hub **konfigurera** fliken. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. |
| Principnyckel för Event hub | Den delade åtkomstnyckeln som används för att autentisera åtkomsten till händelsehubbens namnområde. |
| Partitionsnyckelkolumn | Valfri. En kolumn som innehåller Partitionsnyckeln för event hub-utdata. |
| Händelseserialiseringsformat | Serialiseringsformat för utdata. JSON-, CSV- och Avro stöds. |
| Kodning | För CSV och JSON är UTF-8 i kodningsformat som endast stöds just nu. |
| Avgränsare | Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering av data i CSV-format. Värden som stöds är kommatecken, semikolon, utrymme, fliken och lodrät stapel. |
| Format | Gäller endast för JSON-serialisering. **Radseparerade** anger att utdata formateras genom att låta varje JSON-objekt avgränsas med en ny rad. **Matris** anger att utdata formateras som en matris av JSON-objekt. Den här matrisen stängs först när jobbet stoppas eller Stream Analytics har gått vidare till nästa tidsfönstret. I allmänhet är det bättre att använda rad kommaavgränsade JSON, eftersom det inte krävs någon särskild hantering när utdatafilen skrivs fortfarande till. |
| Egenskapskolumner | Valfri. Fil med kommaavgränsade kolumner som ska anslutas som användaregenskaper för det utgående meddelandet i stället för nyttolasten. Mer information om den här funktionen finns i avsnittet [anpassad metadataegenskaper för utdata](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI
Du kan använda [Power BI](https://powerbi.microsoft.com/) som utdata för ett Stream Analytics-jobb att tillhandahålla för en omfattande visualiseringsupplevelsen av analysresultat. Du kan använda den här funktionen för driftsinstrumentpaneler, rapportgenerering och mått-drivna rapportering.

Power BI-utdata från Stream Analytics är för närvarande inte tillgänglig i regionerna för Azure Tyskland (T-Systems International) och Azure Kina (21Vianet).

### <a name="authorize-a-power-bi-account"></a>Auktorisera en Power BI-konto
1. När Power BI är markerat som utdata i Azure-portalen uppmanas du att auktorisera en befintlig Power BI-användare eller för att skapa en ny Power BI-konto.
   
   ![Auktorisera en Power BI-användare att konfigurera utdata](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2. Skapa ett nytt konto om du ännu inte har någon och välj sedan **auktorisera nu**. Följande sida visas:
   
   ![Autentisera till Power BI från Azure-konto](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3. Ange arbets-eller skolkonto för auktorisering av Power BI-utdata. Om du inte redan har loggat för Power BI, väljer **registrera dig nu**. Det arbeta- eller skolkonto konto som du använder för Power BI kan skilja sig från det konto för Azure-prenumeration som du har nu loggat in med.

### <a name="configure-the-power-bi-output-properties"></a>Konfigurera egenskaper för Power BI-utdata
När du har Power BI-konto som har autentiserats kan konfigurera du egenskaperna för Power BI-utdata. I följande tabell visas egenskapsnamn och deras beskrivningar för att konfigurera din Power BI-utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ange ett eget namn som används i frågor för att dirigera utdata till Power BI-utdata. |
| Grupparbetsyta |Om du vill aktivera delning av data med andra Power BI-användare, kan du välja grupper i Power BI-kontot eller välj **Min arbetsyta** om du inte vill att skriva till en grupp. Uppdaterar en befintlig grupp kräver förnya Power BI-autentisering. |
| Namn på datauppsättning |Ange ett namn på datauppsättning som du vill använda Power BI-utdata. |
| Tabellnamn |Ange ett tabellnamn under datauppsättningen för Power BI-utdata. Power BI-utdata från Stream Analytics-jobb kan för närvarande kan ha endast en tabell i en datauppsättning. |

En genomgång på hur du konfigurerar en Power BI-utdata och en instrumentpanel finns i den [Azure Stream Analytics och Power BI](stream-analytics-power-bi-dashboard.md) artikeln.

> [!NOTE]
> Inte uttryckligen skapa datauppsättningen och tabellen i Power BI-instrumentpanelen. Datauppsättningen och tabellen fylls i automatiskt när jobbet startas och jobbet startar pumpande utdata till Power BI. Om jobbet frågan inte genererar några resultat, skapas inte datauppsättningen och tabellen. Om Power BI har redan en datauppsättning och en tabell med samma namn som det som angavs i den här Stream Analytics-jobb, skrivs den befintliga informationen.
>

### <a name="create-a-schema"></a>Skapa ett schema
Azure Stream Analytics skapar en Power BI-datauppsättning och tabellschemat för användaren om de inte redan finns. I annat fall uppdateras tabellen med nya värden. För närvarande kan endast en tabell finnas i en datauppsättning. 

Powerbi använder först in, skickas bevarandeprincipen. Data samlar in i en tabell tills den når 200 000 rader.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Konvertera en datatyp från Stream Analytics till Power BI
Azure Stream Analytics uppdaterar datamodellen dynamiskt vid körning om utdata-schemat ändras. Alla spåras kolumnen namnändringar, kolumnen ändras, och tillägg och borttagning av kolumner.

Den här tabellen beskriver datatypkonverteringar från [Stream Analytics-datatyper](https://msdn.microsoft.com/library/azure/dn835065.aspx) till Power BI [Entity Data Model (EDM) typer](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model), om en Power BI-datauppsättning och en tabell som inte finns.

Från Stream Analytics | Power BI
-----|-----
bigint | Int64
nvarchar(max) | Sträng
datetime | DateTime
flyt | Double-värde
Post-matris | String typ, konstantvärde ”IRecord” eller ”IArray”

### <a name="update-the-schema"></a>Uppdatera schemat
Stream Analytics härleder modellschemat data baserat på den första uppsättningen händelser i utdata. Senare, om det behövs uppdateras modellschemat som data för att hantera inkommande händelser som inte kanske passar i den ursprungliga scheman.

Undvika de `SELECT *` fråga för att förhindra dynamisk schemauppdatering på rader. Det kan resultera i osäkerheten på om den tid det tar för resultat utöver potentiella konsekvenser för prestanda. Välj de exakta fält som behöver som ska visas på Power BI-instrumentpanelen. Datavärdena bör dessutom vara kompatibel med datatypen.


Föregående/aktuell | Int64 | Sträng | DateTime | Double-värde
-----------------|-------|--------|----------|-------
Int64 | Int64 | Sträng | Sträng | Double-värde
Double-värde | Double-värde | Sträng | Sträng | Double-värde
Sträng | Sträng | Sträng | Sträng | Sträng 
DateTime | Sträng | Sträng |  DateTime | Sträng


### <a name="renew-power-bi-authorization"></a>Förnya auktoriseringen för Power BI
Om lösenordet för Power BI-kontot ändras när ditt Stream Analytics-jobb skapades eller senast autentiserade, måste du autentiseras på nytt Stream Analytics. Om Azure Multi-Factor Authentication har konfigurerats på din Azure Active Directory (Azure AD)-klient, måste du också förnya auktoriseringen för Power BI varannan vecka. Ett symtom på det här problemet är ingen jobbutdata och en ”autentisera användaren error” i loggarna för åtgärden:

  ![Powerbi autentisera användarfel](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

Stoppa körs jobbet för att lösa problemet och gå till Power BI-utdata. Välj den **förnya auktoriseringen** länka och starta om jobbet från den **senast stoppad** att undvika dataförlust.

  ![Förnya auktoriseringen för Power BI för utdata](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## <a name="table-storage"></a>Table Storage
[Azure tabellagring](../storage/common/storage-introduction.md) erbjuder hög tillgänglighet och mycket skalbar lagring, så att ett program kan anpassas automatiskt efter användarens behov. Table storage är Microsofts NoSQL nyckel-och attributdatabas, som du kan använda för strukturerade data med färre begränsningar i schemat. Azure Table storage kan användas för att lagra data för persistence och effektiv Filhämtning.

I följande tabell visas egenskapsnamn och deras beskrivningar för att skapa ett tabellutdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera utdata till table storage. |
| Lagringskonto |Namnet på det lagringskonto där du skickar dina utdata. |
| Lagringskontonyckel |Åtkomstnyckeln som är associerade med lagringskontot. |
| Tabellnamn |Tabellens namn. Tabellen skapas om det inte finns. |
| Partitionsnyckeln |Namnet på utdatakolumnen som innehåller Partitionsnyckeln. Partitionsnyckeln är en unik identifierare för partitionen i en tabell som utgör den första delen av en entitets primärnyckel. Det är ett strängvärde som kan vara upp till 1 KB stora. |
| Radnyckel |Namnet på utdatakolumnen som innehåller Radnyckeln. Radnyckeln är en unik identifierare för en entitet i en partition. Den utgör den andra delen av en entitets primärnyckel. Radnyckeln är ett strängvärde som kan vara upp till 1 KB stora. |
| Batchstorlek |Antalet poster för en batchåtgärd. Standardvärdet (100) är tillräckliga för de flesta jobb. Se den [tabell batchåtgärd spec](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) för mer information om hur du ändrar den här inställningen. |

## <a name="service-bus-queues"></a>Service Bus-köer
[Service Bus-köer](https://msdn.microsoft.com/library/azure/hh367516.aspx) erbjuder en FIFO meddelandeleverans till en eller flera konkurrerande konsumenter. Meddelanden tas vanligtvis emot och bearbetas av mottagarna i den ordning som de har lagts till i kön. Varje meddelande tas emot och bearbetas av endast en meddelandekonsument.

I följande tabell visas egenskapsnamn och deras beskrivningar för att skapa en kö-utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera utdata till det här Service Bus-kö. |
| Service Bus-namnområde |En behållare för en uppsättning meddelandeentiteter. |
| Könamn |Namnet på Service Bus-kö. |
| Köprincipnamn |När du skapar en kö, kan du också skapa principer för delad åtkomst för kön **konfigurera** fliken. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. |
| Köprincipnyckel |Den delade åtkomstnyckeln som används för att autentisera åtkomsten till Service Bus-namnområdet. |
| Händelseserialiseringsformat |Serialiseringsformat för utdata. JSON-, CSV- och Avro stöds. |
| Kodning |För CSV och JSON är UTF-8 i kodningsformat som endast stöds just nu. |
| Avgränsare |Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering av data i CSV-format. Värden som stöds är kommatecken, semikolon, utrymme, fliken och lodrät stapel. |
| Format |Gäller endast för JSON-typen. **Radseparerade** anger att utdata formateras genom att låta varje JSON-objekt avgränsas med en ny rad. **Matris** anger att utdata formateras som en matris av JSON-objekt. |
| Egenskapskolumner | Valfri. Fil med kommaavgränsade kolumner som ska anslutas som användaregenskaper för det utgående meddelandet i stället för nyttolasten. Mer information om den här funktionen finns i avsnittet [anpassad metadataegenskaper för utdata](#custom-metadata-properties-for-output). |

Antalet partitioner är [baserat på Service Bus-SKU och storleken](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckeln är ett heltalsvärde som unikt för varje partition.

## <a name="service-bus-topics"></a>Service Bus-ämnen
Service Bus-köer ger en 1: 1-kommunikationsmetod från sändare till mottagare. [Service Bus-ämnen](https://msdn.microsoft.com/library/azure/hh367516.aspx) en en-till-många-formen av kommunikation.

I följande tabell visas egenskapsnamn och deras beskrivningar för att skapa ett ämne på utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera frågeresultatet visas på det här Service Bus-ämnet. |
| Service Bus-namnområde |En behållare för en uppsättning meddelandeentiteter. När du har skapat en ny händelsehubb, skapade du även en Service Bus-namnrymd. |
| Ämnesnamn |Ämnen är meddelandeentiteter, liknande event hubs och köer. De har skapats för att samla in händelseströmmar från enheter och tjänster. När ett ämne har skapats, har nu fått ett visst namn. Meddelanden som skickats till ett ämne är inte tillgängliga om inte en prenumeration skapas, så se till att det finns en eller flera prenumerationer under avsnittet. |
| Ämnesprincipnamn |När du skapar ett ämne kan du också skapa principer för delad åtkomst på avsnittet **konfigurera** fliken. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. |
| Ämnesprincipnyckel |Den delade åtkomstnyckeln som används för att autentisera åtkomsten till Service Bus-namnområdet. |
| Händelseserialiseringsformat |Serialiseringsformat för utdata. JSON-, CSV- och Avro stöds. |
| Kodning |Om du använder CSV eller JSON-format, måste en kodning anges. UTF-8 är det enda kodformat som stöds för närvarande. |
| Avgränsare |Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering av data i CSV-format. Värden som stöds är kommatecken, semikolon, utrymme, fliken och lodrät stapel. |
| Egenskapskolumner | Valfri. Fil med kommaavgränsade kolumner som ska anslutas som användaregenskaper för det utgående meddelandet i stället för nyttolasten. Mer information om den här funktionen finns i avsnittet [anpassad metadataegenskaper för utdata](#custom-metadata-properties-for-output). |

Antalet partitioner är [baserat på Service Bus-SKU och storleken](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckeln är ett heltalsvärde som unikt för varje partition.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) är en globalt distribuerad databastjänst som erbjuder obegränsad elastisk skalning runt hela världen, frågekörning och den automatiska indexeringen över schemaoberoende datamodeller. Mer information om alternativ för Azure Cosmos DB-samling för Stream Analytics, finns det [Stream Analytics med Azure Cosmos DB som utdata](stream-analytics-documentdb-output.md) artikeln.

Azure Cosmos DB-utdata från Stream Analytics är för närvarande inte tillgänglig i regionerna för Azure Tyskland (T-Systems International) och Azure Kina (21Vianet).

> [!Note]
> För närvarande stöder Azure Stream Analytics endast anslutning till Azure Cosmos DB med hjälp av SQL-API.
> Andra Azure Cosmos DB API: er stöds inte ännu. Om du punkt Azure Stream Analytics till Azure Cosmos DB-konton som skapats med andra API: er, kanske data inte korrekt lagras.

I följande tabell beskrivs egenskaperna för att skapa ett Azure Cosmos DB-utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias | Ett alias till se detta utdata i din Stream Analytics-fråga. |
| Kanalmottagare | Azure Cosmos DB. |
| Importalternativ | Välj antingen **Välj Cosmos DB från prenumerationen** eller **ange Cosmos DB-inställningar manuellt**.
| Konto-ID | Namn eller slutpunkten URI: N för Azure Cosmos DB-kontot. |
| Kontonyckel | Den delade åtkomstnyckeln för Azure Cosmos DB-kontot. |
| Databas | Namnet på Azure Cosmos DB-databasen. |
| Samlingsnamnsmönster | Namnet på samlingen eller ett mönster för samlingar som ska användas. <br />Du kan skapa samlingsnamnsformatet med hjälp av valfritt {partition}-token, där partitionerna börjar från 0. Två exempel:  <br /><ul><li> _MyCollection_: En samling som heter ”MyCollection” måste finnas.</li>  <li> _MyCollection{partition}_: Baserat på partitioneringskolumnen.</li></ul> Partitionering kolumnen samlingarna måste vara uppfyllda: ”MyCollection0”, ”MyCollection1”, ”MyCollection2”, och så vidare. |
| Partitionsnyckeln | Valfri. Du behöver detta endast om du använder en token för {partition} i din samlingsnamnsmönstret.<br /> Partitionsnyckeln är namnet på fältet i utdatahändelserna som används för att specificera nyckeln för att partionera utdata över samlingarna.<br /> Du kan använda valfri godtycklig utdatakolumnen för enskild samling utdata. Ett exempel är PartitionId. |
| Dokument-id |Valfri. Namnet på fältet i utdatahändelserna som används för att ange den primära nyckeln för vilka insert eller uppdateringsåtgärder baseras.

## <a name="azure-functions"></a>Azure Functions
Azure Functions är en serverlös beräkningstjänst som du kan använda för att köra kod på begäran utan att behöva installera eller hantera infrastruktur. Du kan implementera kod som utlöses av händelser i Azure eller partner services. Azure Functions kapacitet att reagera på utlösare gör det ett naturligt utdatamål för Azure Stream Analytics. Den här utdataadapter kan användarna ansluta Stream Analytics till Azure Functions och köra ett skript eller kod som svar på en rad olika händelser.

Azure Functions-utdata från Stream Analytics är för närvarande inte tillgänglig i regionerna för Azure Tyskland (T-Systems International) och Azure Kina (21Vianet).

Azure Stream Analytics anropar Azure Functions via HTTP-utlösare. Utdataadaptern för Azure är tillgängligt med konfigurerbara egenskaper som är följande:

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Funktionsapp |Namnet på din Azure Functions-app. |
| Funktion |Namnet på funktionen i Azure Functions-app. |
| Nyckel |Om du vill använda en Azure-funktion från en annan prenumeration kan du göra det genom att tillhandahålla nyckel för att få åtkomst till din funktion. |
| Max batchstorlek |En egenskap som kan du ange den maximala storleken för varje utdatabatch som skickas till din Azure-funktion. Inkommande enheten är i byte. Som standard är det här värdet 262 144 byte (256 KB). |
| Max batchantal  |En egenskap som kan du ange det maximala antalet händelser i varje batch som skickas till Azure Functions. Standardvärdet är 100. |

När Azure Stream Analytics tar emot en 413 (”http begär enhet är stor”) undantag från en Azure-funktion, den minskar storleken på batcharna som skickas till Azure Functions. Använd det här undantaget för att se till att Azure Stream Analytics inte skickar för stora batchar i Azure function-koden. Kontrollera också att Maximal batchstorlek antal och storlek värden som används i funktionen stämmer överens med de värden som anges i Stream Analytics-portalen.

I en situation där det finns ingen händelse som hamnar under en tidsperiod, inga utdata skapas. Därför kan den **computeResult** inte anropa funktionen. Det här beteendet är konsekvent med de inbyggda mängdfunktionerna.

## <a name="custom-metadata-properties-for-output"></a>Anpassade metadataegenskaper för utdata 

Du kan koppla fråga kolumner som användaregenskaper till meddelanden. Det går inte dessa kolumner i nyttolasten. Egenskaperna finns i form av en ordlista för utdata-meddelandet. *Nyckeln* är column-name och *värdet* är kolumnvärdet för i egenskapsordlista. Alla Stream Analytics-datatyper stöds utom post och matris.  

Stöds utdata: 
* Service Bus-kö 
* Service Bus-ämne 
* Händelsehubb 

I följande exempel vi lägga till de två fälten `DeviceId` och `DeviceStatus` av metadata. 
* Fråga: `select *, DeviceId, DeviceStatus from iotHubInput`
* Konfiguration av utdata: `DeviceId,DeviceStatus`

![Egenskapskolumner](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

Följande skärmbild visar utdata meddelandeegenskaper granskas i EventHub via [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

![Anpassade egenskaper för händelse](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>Partitionering

I följande tabell sammanfattas partition-stöd och antalet skrivare för utdata för varje Utdatatyp av:

| Utdatatyp | Partitionering | Partitionsnyckeln  | Antalet utdata-skrivare |
| --- | --- | --- | --- |
| Azure Data Lake Store | Ja | Använd {date} och {time}-token i prefixmönster för sögväg. Välj datumformat, till exempel ÅÅÅÅ/MM/DD, DD/MM/ÅÅÅÅ eller MM-DD-ÅÅÅÅ. HH används för tidsformatet. | Följer inkommande partitionering för [helt kan frågor](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Ja | Baserat på PARTITION BY-sats i frågan. | Följer inkommande partitionering för [helt kan frågor](stream-analytics-scale-jobs.md). Läs mer om att uppnå bättre skrivprestanda dataflöde när du läser in data till Azure SQL Database i [Azure Stream Analytics-utdata till Azure SQL Database](stream-analytics-sql-output-perf.md). |
| Azure Blob Storage | Ja | Använd {date} och {time}-token från dina event fält i mönstret för sökvägen. Välj datumformat, till exempel ÅÅÅÅ/MM/DD, DD/MM/ÅÅÅÅ eller MM-DD-ÅÅÅÅ. HH används för tidsformatet. BLOB-utdata kan partitioneras av en enda anpassad händelseattribut {fieldname} eller {datetime:\<specificerare >}. | Följer inkommande partitionering för [helt kan frågor](stream-analytics-scale-jobs.md). |
| Azure Event Hubs | Ja | Ja | Varierar beroende på partitionen justering.<br /> När Partitionsnyckeln för event hub utdata justeras lika med den överordnade (föregående) frågesteg, är antalet skrivare samma som antalet partitioner i event hub-utdata. Varje skrivare använder den [EventHubSender klass](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) att skicka händelser till en specifik partition. <br /> När Partitionsnyckeln för event hub utdata inte är justerad med den överordnade (föregående) frågesteg, är antalet skrivare samma som antalet partitioner i det föregående steget. Varje skrivare använder den [SendBatchAsync klass](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) i **EventHubClient** att skicka händelser till utdata-partitionerna. |
| Power BI | Nej | Ingen | Ej tillämpligt. |
| Azure Table Storage | Ja | Alla utdatakolumn.  | Följer inkommande partitionering för [fullständigt parallelliseras frågor](stream-analytics-scale-jobs.md). |
| Azure Service Bus-ämne | Ja | Valt automatiskt. Antalet partitioner är baserad på den [Service Bus-SKU och storlek](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckeln är ett heltalsvärde som unikt för varje partition.| Samma som antalet partitioner i avsnittet om utdata.  |
| Azure Service Bus-kö | Ja | Valt automatiskt. Antalet partitioner är baserad på den [Service Bus-SKU och storlek](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckeln är ett heltalsvärde som unikt för varje partition.| Samma som antalet partitioner i den utgående kön. |
| Azure Cosmos DB | Ja | Använd {partition}-token i samlingsnamnsmönstret. Värdet för {partition} baseras på PARTITION BY-sats i frågan. | Följer inkommande partitionering för [fullständigt parallelliseras frågor](stream-analytics-scale-jobs.md). |
| Azure Functions | Nej | Ingen | Ej tillämpligt. |

Om nätverkskortet utdata inte är partitionerad medför brist på data i en indatapartitionen en fördröjning upp till sent ankomst lång tid. I sådana fall kan sammanfogas utdata till en enda skrivare, vilket kan orsaka flaskhalsar i din pipeline. Mer information om principen för sent ankomst finns [Azure Stream Analytics händelse ordning överväganden](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Batchstorlek för utdata
Azure Stream Analytics använder variabel storlek batchar att bearbeta händelser och skriva till utdata. Vanligtvis Stream Analytics-motor skriva inte ett meddelande i taget och använder batchar för effektivitet. När frekvensen för både inkommande och utgående händelser är hög använder Stream Analytics större batchar. När den utgående hastigheten är låg, använder mindre Batcher för att hålla det med låg latens.

I följande tabell beskrivs några av överväganden för batchbearbetning av utdata:

| Utdatatyp | Maxstorlek på meddelande | Optimering av batch-storlek |
| :--- | :--- | :--- |
| Azure Data Lake Store | Se [Data Lake Storage begränsar](../azure-subscription-service-limits.md#data-lake-store-limits). | Använd upp till 4 MB per skrivning. |
| Azure SQL Database | 10 000 maximalt antal rader per enskild massinfogning.<br />100 minsta rader per enskild massinfogning. <br />Se [Azure SQL begränsar](../sql-database/sql-database-resource-limits.md). |  Varje batch är inledningsvis bulk infogas med Maximal batchstorlek. Du kan dela batch i hälften (tills du når den minsta batchstorleken) baserat på återförsökbart fel från SQL. |
| Azure Blob Storage | Se [Azure Storage begränsar](../azure-subscription-service-limits.md#storage-limits). | Maximal blob blockstorleken är 4 MB.<br />Maximal blob bock antalet är 50 000. |
| Azure Event Hubs  | 256 KB per meddelande. <br />Se [Event Hubs begränsar](../event-hubs/event-hubs-quotas.md). |  När indata/utdata partitionering inte är justerad varje händelse är späckad individuellt i **EventData** och skickas i en batch med upp till den maximala meddelandestorleken (1 MB för Premium-SKU). <br /><br />  När indata/utdata partitionering justeras flera händelser är förpackade i en enda **EventData** instans, upp till den maximala meddelandestorleken och skickas.  |
| Power BI | Se [Power BI Rest API begränsar](https://msdn.microsoft.com/library/dn950053.aspx). |
| Azure Table Storage | Se [Azure Storage begränsar](../azure-subscription-service-limits.md#storage-limits). | Standardvärdet är 100 entiteter per enskild transaktion. Du kan konfigurera det till ett mindre värde efter behov. |
| Azure Service Bus-kö   | 256 KB per meddelande.<br /> Se [Service Bus begränsar](../service-bus-messaging/service-bus-quotas.md). | Använda en enda händelse per meddelande. |
| Azure Service Bus-ämne | 256 KB per meddelande.<br /> Se [Service Bus begränsar](../service-bus-messaging/service-bus-quotas.md). | Använda en enda händelse per meddelande. |
| Azure Cosmos DB   | Se [Azure Cosmos DB begränsar](../azure-subscription-service-limits.md#azure-cosmos-db-limits). | Batchstorlek och skriva frekvens justeras dynamiskt baserat Azure Cosmos DB-svar. <br /> Det finns inga förinställt begränsningar från Stream Analytics. |
| Azure Functions   | | Standardstorleken för batch är 262 144 byte (256 KB). <br /> Standardvärdet för händelseantal per batch är 100. <br /> Batchstorlek är konfigurerbar och kan ökas eller minskas i Stream Analytics [Utdataalternativ](#azure-functions).

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> 
> [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

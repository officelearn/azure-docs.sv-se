---
title: Förstå utdata från Azure Stream Analytics
description: Den här artikeln beskriver data utdata alternativen som finns i Azure Stream Analytics, inklusive Power BI för analysresultat.
services: stream-analytics
author: jasonwhowell
ms.author: mamccrea
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: abf581430f7cf7020145b0217c387b8c2fc4f795
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979411"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Förstå utdata från Azure Stream Analytics
Den här artikeln beskrivs de olika typerna av utdata som är tillgängliga för Azure Stream Analytics-jobb. Utdata kan du lagra och spara resultatet av Stream Analytics-jobb. Med utdata kan du göra ytterligare affärsanalys och datalager för dina data. 

När du utformar din Stream Analytics-fråga, referera till namnet på den utdata med hjälp av den [INTO-sats](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics). Du kan använda ett enda utflöde per jobb eller flera utdata per direktuppspelningsjobbet om du behöver genom att tillhandahålla flera INTO-satser i fråga.

Skapa, redigera och testa Stream Analytics-jobbet matar ut, du kan använda den [Azure-portalen](stream-analytics-quick-create-portal.md#configure-output-to-the-job), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.Net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output), och [Visual Studio](stream-analytics-quick-create-vs.md).

Vissa typer av stöd för utdata [partitionering](#partitioning), och [utdata batch storlekar](#output-batch-size) variera för att optimera genomflödet.


## <a name="azure-data-lake-store"></a>Azure Data Lake Store
Stream Analytics stöder [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store är en företagsomfattande storskalig lagringsplats för analytiska arbetsbelastningar för stordata. Data Lake Store kan du lagra data i valfri storlek, typ och inmatningshastighet för driftsanalyser och undersökande analyser. Stream Analytics måste ha behörighet att komma åt Data Lake Store.

Azure Data Lake Store-utdata från Stream Analytics är för närvarande inte tillgänglig i regionerna för Azure Tyskland (T-Systems International) och Azure Kina (21Vianet).

### <a name="authorize-an-azure-data-lake-store-account"></a>Auktorisera ett Azure Data Lake Store-konto

1. När Data Lake Storage har valts som utdata i Azure-portalen uppmanas du att auktorisera en anslutning till en befintlig Data Lake Store.  

   ![Auktorisera Data Lake Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

2. Om du redan har åtkomst till Data Lake Store väljer **auktorisera nu** och en sida öppnas som anger **omdirigering till auktorisering**. När auktoriseringen har slutförts visas sidan där du kan konfigurera Data Lake Store-utdata.

3. När du har Data Lake Store-konto som autentiseras kan konfigurera du egenskaperna för Data Lake Store-utdata. Tabellen nedan är en lista över egenskapsnamn och deras beskrivning för att konfigurera din Data Lake Store-utdata.

   ![Auktorisera Data Lake Store](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

| Egenskapsnamn | Beskrivning | 
| --- | --- |
| Utdataalias | Ett eget namn som används i frågor för att dirigera utdata till det här Data Lake Store. | 
| Kontonamn | Namnet på Data Lake Storage-konto där du skickar dina utdata. Visas med en nedrullningsbar lista över Data Lake Store-konton som är tillgängliga i din prenumeration. |
| Prefixmönster för sögväg | Filsökvägen som används för att skriva dina filer inom det angivna Data Lake Store-kontot. Du kan ange en eller flera instanser av den {date} och {time} variabler.</br><ul><li>Exempel 1: mapp1/logs / {date} / {time}</li><li>Exempel 2: mapp1/logs / {date}</li></ul><br>Tidsstämpel för mappstrukturen skapats följer UTC-tid och inte lokal tid.</br><br>Om filen sökvägsmönster innehåller inte en avslutande ”/”, senaste mönstret i sökvägen behandlas som ett filnamnsprefix. </br></br>Nya filer skapas i dessa fall:<ul><li>Ändra i utdata-schemat</li><li>Extern eller intern omstart av ett jobb.</li></ul> |
| Datumformat | Valfri. Du kan välja datumformat där filerna ordnas om datumtoken används i prefixsökvägen. Exempel: ÅÅÅÅ/MM/DD |
|Tidsformat | Valfri. Om tiden token används i prefixsökvägen, ange tidsformatet där filerna ordnas. Det enda värdet som stöds är för närvarande HH. |
| Händelseserialiseringsformat | Serialiseringsformat för utdata. JSON-, CSV- och Avro stöds.| 
| Kodning | Om du använder CSV eller JSON-format, måste en kodning anges. UTF-8 är det enda kodformat som stöds för närvarande.|
| Avgränsare | Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering av CSV-data. Värden som stöds är kommatecken, semikolon, mellanslag, fliken och lodrät stapel.|
| Format | Gäller endast för JSON-serialisering. Radseparering innebär att utdata formateras genom att låta varje JSON-objekt avgränsas med en ny rad. Matrisen anger att utdata formateras som en matris av JSON-objekt. Den här matrisen stängs först när jobbet stoppas eller Stream Analytics har gått vidare till nästa tidsfönstret. I allmänhet är det bättre att använda rad avgränsade JSON, eftersom det inte krävs någon särskild hantering när utdatafilen skrivs fortfarande till.|

### <a name="renew-data-lake-store-authorization"></a>Förnya auktoriseringen för Data Lake Store
Du måste autentiseras på nytt Data Lake Store-konto om lösenordet har ändrats sedan jobbet skapades eller senast autentiserade. Om du inte återautentisera jobbet producerar inte utdata resultatet och visar ett felmeddelande om att behovet av omauktorisering i loggarna för åtgärden. För närvarande finns en begränsning där autentiseringstoken behöver uppdateras manuellt efter 90 dagar för alla jobb med Data Lake Store-utdata. 

Förnya auktorisering, **stoppa** jobbet > Gå till ditt Data Lake Store-utdata > klickar du på den **förnya auktoriseringen** länka och under en kort tid en sida kommer att visas som anger **omdirigering till auktorisering...** . Sidan stängs automatiskt och om det lyckas anger **auktoriseringen har förnyats**. Du måste sedan klicka på **spara** längst ned på sidan och kan gå vidare genom att starta om jobbet från den **senast stoppad** att undvika dataförlust.

![Auktorisera Data Lake Store](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  

## <a name="sql-database"></a>SQL Database
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) kan användas som utdata för data som är relationell natur och för program som är beroende av innehållet finns i en relationsdatabas. Stream Analytics-jobb att skriva till en befintlig tabell i en Azure SQL Database.  Tabellschemat måste exakt matcha fälten och deras typer som utdata från jobbet. En [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) kan också anges som utdata via SQL-databas output-alternativet samt. Mer information om hur du kan förbättra genomströmning för skrivning, referera till den [Stream Analytics med Azure SQL DB som utdata](stream-analytics-sql-output-perf.md) artikeln. I tabellen nedan visas vilka egenskapsnamn och deras beskrivning för att skapa en SQL Database-utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera utdata till den här databasen. |
| Databas | Namnet på databasen där du skickar dina utdata. |
| servernamn | SQL Database-servernamn. |
| Användarnamn | Användarnamnet som har åtkomst till att skriva till databasen. |
| Lösenord | Lösenordet för att ansluta till databasen |
| Tabell | Tabellnamnet där skrivs utdata. Tabellnamnet är skiftlägeskänsligt och schemat för den här tabellen ska matcha exakt med antalet fält och deras typer som skapas av din jobbutdata. |

> [!NOTE]
> Azure SQL Database-erbjudande är för närvarande en jobbutdata i Stream Analytics. Men stöds en Azure virtuell dator som kör SQL Server med en-databas ansluten inte. Detta kan ändras i framtida versioner.
> 

## <a name="blob-storage"></a>Blob Storage
BLOB-lagring erbjuder en kostnadseffektiv och skalbar lösning för att lagra stora mängder Ostrukturerade data i molnet.  En introduktion i Azure Blob storage och dess användning finns i dokumentationen på [hur du använder Blobar](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

I tabellen nedan visas vilka egenskapsnamn och deras beskrivning för att skapa en blob-utdata.

| Egenskapsnamn       | Beskrivning                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Utdataalias        | Ett eget namn som används i frågor för att dirigera utdata till blob storage. |
| Lagringskonto     | Namnet på det lagringskonto där du skickar dina utdata.               |
| Lagringskontonyckel | Den hemliga nyckeln som är associerade med lagringskontot.                              |
| Storage-behållare   | Behållare är en logisk gruppering för blobbar som lagras i Microsoft Azure Blob-tjänsten. När du laddar upp en blob till Blob-tjänsten måste du ange en behållare för blobben. |
| Sökvägsmönster | Valfri. Filmönster för sökvägen som används för att skriva dina blobbar i den angivna behållaren. <br /><br /> Sökvägsmönster, kanske du väljer att använda en eller flera instanser av variabler för datum-tid för att ange den frekvens som BLOB-objekt skrivs: <br /> {date}, {time} <br /><br />Om du använder det här [Azure portal-länk](https://portal.azure.com/?microsoft_azure_streamanalytics_bloboutputpathpartitioning=true&Microsoft_Azure_StreamAnalytics_bloboutputcontainerpartitioning=true) för att komma åt den anpassade blob partitionering förhandsversion, kan du ange en anpassad {fältet} namn från dina händelsedata till BLOB-partitionen. Fältnamnet är alfanumeriska och kan innehålla blanksteg, bindestreck och understreck. Följande: begränsningar för anpassade fält <ul><li>Fall insensitivity (det går inte att skilja mellan kolumnen ”ID” och kolumnen ”id”)</li><li>Kapslade fält tillåts inte (i stället använda ett alias i jobbet frågan ”platta ut” fältet)</li><li>Uttryck kan inte användas som ett fältnamn.</li></ul> <br /><br /> Förhandsversionen kan också användningen av anpassade datum/tid-format specificerare konfigurationer i sökvägen. Anpassat datum och tid format måste vara angivna en i taget, omgivna av den {datetime:\<specificerare >} nyckelord. Tillåten indata \<specificerare > är åååå, MM, M, dd, d, HH, H, mm, m, ss eller s. De {datetime:\<specificerare >} nyckelordet kan användas flera gånger i sökvägen för att skapa anpassade datum/tid-konfigurationer. <br /><br />Exempel: <ul><li>Exempel 1: cluster1/logs / {date} / {time}</li><li>Exempel 2: cluster1/logs / {date}</li><li>Exempel 3 (förhandsversion): cluster1 / {client_id} / {date} / {time}</li><li>Exempel 4 (förhandsversion): cluster1 / {datetime:ss} / {myField} där frågan är: Välj data.myField som myField från indata;</li><li>Exempel 5 (förhandsversion): cluster1/år = {datetime:yyyy} / månad = {datetime:MM} per dag = {datetime:dd}</ul><br /><br />Tidsstämpel för mappstrukturen skapats följer UTC-tid och inte lokal tid.<br /><br/>Namngivning av följande enligt följande konvention: <br /><br />{Path Prefix Pattern}/schemaHashcode_Guid_Number.extension<br /><br />Exempel utdatafilerna:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br /><br /> Mer information om den här förhandsversionen finns [anpassad DateTime sökvägsmönster för Azure Stream Analytics blob storage-utdata (förhandsversion)](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Datumformat | Valfri. Du kan välja datumformat där filerna ordnas om datumtoken används i prefixsökvägen. Exempel: ÅÅÅÅ/MM/DD |
| Tidsformat | Valfri. Om tiden token används i prefixsökvägen, ange tidsformatet där filerna ordnas. Det enda värdet som stöds är för närvarande HH. |
| Händelseserialiseringsformat | Serialiseringsformat för utdata.  JSON-, CSV- och Avro stöds. |
| Kodning    | Om du använder CSV eller JSON-format, måste en kodning anges. UTF-8 är det enda kodformat som stöds för närvarande. |
| Avgränsare   | Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering av CSV-data. Värden som stöds är kommatecken, semikolon, utrymme, fliken och lodrät stapel. |
| Format      | Gäller endast för JSON-serialisering. Radseparering innebär att utdata formateras genom att låta varje JSON-objekt avgränsas med en ny rad. Matrisen anger att utdata formateras som en matris av JSON-objekt. Den här matrisen stängs först när jobbet stoppas eller Stream Analytics har gått vidare till nästa tidsfönstret. I allmänhet är det bättre att använda rad avgränsade JSON, eftersom det inte krävs någon särskild hantering när utdatafilen skrivs fortfarande till. |

När du använder blob-lagring som utdata, skapas en ny fil i blob i följande fall:

* Om filen överskrider det maximala antalet tillåtna block (för närvarande 50 000). Det maximala antalet tillåtna block kan nås utan att nå den högsta tillåtna blobstorleken. Till exempel om frekvensen av utgående är hög, du kan se mer byte per block och filstorleken är större. Om frekvensen av utgående är låg, varje block har mindre data och filstorleken är mindre.
* Om det finns en schemaändring i utdata och utdataformat kräver fast schema (CSV och Avro).  
* Om ett jobb startas antingen externt av en användare stoppa den och sedan starta den eller internt för återställning av underhåll eller fel.  
* Om frågan är partitionerad helt skapas ny fil för varje partition i utdata.  
* Om en fil eller en behållare för lagringskontot tas bort av användaren.  
* Om utdata är tid som har partitionerats med hjälp av prefixmönster för sögväg, används en ny blob när frågan flyttar till nästa timma.
* Om utdata har partitionerats med ett anpassat fält, skapas en ny blob per partitionsnyckel om den inte finns.
* Om utdata har partitionerats med ett anpassat fält där partition nyckelns kardinalitet överskrider 8000, skapas en ny blob per partitionsnyckel.

## <a name="event-hub"></a>Händelsehubb
Den [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) service är en mycket skalbar lösning för publicering-prenumeration händelse. Det kan samla in miljontals händelser per sekund. En användning av en Event Hub som utdata är när resultatet av ett Stream Analytics-jobb blir indata för en annan strömningsuppgift.

Det finns några parametrar som behövs för att konfigurera Event Hub-dataströmmar som utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias | Ett eget namn som används i frågor för att dirigera utdata till den här Händelsehubben. |
| Namnområde för händelsehubb |Ett namnområde för Event Hub är en behållare för en uppsättning meddelandeentiteter. När du har skapat en ny Händelsehubb, skapade du även ett namnområde för Event Hub. |
| Namn på händelsehubb | Namnet på din Event Hub-utdata. |
| Principnamn för Event Hub | Den princip för delad åtkomst som kan skapas på fliken Konfigurera för Event Hub. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. |
| Principnyckel för Event Hub | Den delade åtkomstnyckeln som används för att autentisera åtkomst till namnområdet för Händelsehubben. |
| Partitionsnyckelkolumnen [valfritt] | Den här kolumnen innehåller Partitionsnyckeln för Event Hub-utdata. |
| Händelseserialiseringsformat | Serialiseringsformat för utdata.  JSON-, CSV- och Avro stöds. |
| Kodning | För CSV och JSON är UTF-8 i kodningsformat som endast stöds just nu. |
| Avgränsare | Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering av data i CSV-format. Värden som stöds är kommatecken, semikolon, utrymme, fliken och lodrät stapel. |
| Format | Gäller endast för JSON-serialisering. Radseparering innebär att utdata formateras genom att låta varje JSON-objekt avgränsas med en ny rad. Matrisen anger att utdata formateras som en matris av JSON-objekt. Den här matrisen stängs först när jobbet stoppas eller Stream Analytics har gått vidare till nästa tidsfönstret. I allmänhet är det bättre att använda rad avgränsade JSON, eftersom det inte krävs någon särskild hantering när utdatafilen skrivs fortfarande till. |

## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/) kan användas som utdata för ett Stream Analytics-jobb för att tillhandahålla för en omfattande visualiseringsupplevelsen av analysresultat. Den här funktionen kan användas för driftsinstrumentpaneler, rapportgenerering och mått som driven reporting.

Power BI-utdata från Stream Analytics är för närvarande inte tillgänglig i regionerna för Azure Tyskland (T-Systems International) och Azure Kina (21Vianet).

### <a name="authorize-a-power-bi-account"></a>Auktorisera en Power BI-konto
1. När Power BI är markerat som utdata i Azure-portalen uppmanas du att auktorisera en befintlig Power BI-användare eller för att skapa en ny Power BI-konto.  
   
   ![Auktorisera Power BI-användare](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  

2. Skapa ett nytt konto om du inte ännu har en och sedan klicka på auktorisera nu.  Följande sida visas:
   
   ![Azure-konto Powerbi](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  

3. I det här steget anger du arbets-eller skolkonto för auktorisering av Power BI-utdata. Om du inte redan har loggat för Power BI, väljer du registrering nu. Det arbeta- eller skolkonto konto som du använder för Power BI kan skilja sig från kontot Azure-prenumeration som du är inloggad med.

### <a name="configure-the-power-bi-output-properties"></a>Konfigurera egenskaper för Power BI-utdata
När du har Power BI-konto som har autentiserats kan konfigurera du egenskaperna för Power BI-utdata. Tabellen nedan är listan över egenskapsnamn och deras beskrivning för att konfigurera din Power BI-utdata.

| Egenskapsnamn | beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera utdata till den här Power BI-utdata. |
| Grupparbetsyta |Om du vill aktivera delning av data med andra Power BI-användare kan du välja grupper i Power BI-kontot eller Välj ”Min arbetsyta” om du inte vill skriva till en grupp.  Uppdaterar en befintlig grupp kräver förnya Power BI-autentisering. |
| Namn på datauppsättning |Ange ett namn på datauppsättning som man önskar att använda Power BI-utdata |
| Tabellnamn |Ange ett tabellnamn under datauppsättningen för Power BI-utdata. För närvarande kan kan Power BI-utdata från Stream Analytics-jobb bara ha en tabell i en datauppsättning |

En genomgång av hur du konfigurerar en Power BI-utdata och en instrumentpanel, finns det [Azure Stream Analytics och Power BI](stream-analytics-power-bi-dashboard.md) artikeln.

> [!NOTE]
> Inte uttryckligen skapa datauppsättningen och tabellen i Power BI-instrumentpanelen. Datauppsättningen och tabellen fylls i automatiskt när jobbet startas och jobbet startar pumpande utdata till Power BI. Observera att om jobbfråga inte genererar några resultat, datauppsättningen och tabellen inte skapats. Observera att om Power BI har redan en datauppsättning och en tabell med samma namn som en som tillhandahålls i den här Stream Analytics-jobb, över befintliga data.
> 

### <a name="schema-creation"></a>Skapa en schema
Azure Stream Analytics skapar en Power BI-datauppsättning och en tabell från användare om det inte redan finns. I annat fall uppdateras tabellen med nya värden. Det finns för närvarande en begränsning som endast en tabell kan finnas inom en datauppsättning.

### <a name="data-type-conversion-from-stream-analytics-to-power-bi"></a>Datatypskonverteringen från Stream Analytics till Power BI
Azure Stream Analytics uppdaterar datamodellen dynamiskt vid körning om utdata-schemat ändras. Alla spåras kolumnen namnändringar, kolumnen ändras, och tillägg och borttagning av kolumner.

Den här tabellen beskriver datatypkonverteringar från [Stream Analytics-datatyper](https://msdn.microsoft.com/library/azure/dn835065.aspx) till Power BIs [Entity Data Model (EDM) typer](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) om en POWER BI-datauppsättning och en tabell inte finns.

Från Stream Analytics | Power BI
-----|-----|------------
bigint | Int64
nvarchar(max) | Sträng
datetime | DateTime
flyt | Double-värde
Post-matris | String typ, konstantvärde ”IRecord” eller ”IArray”

### <a name="schema-update"></a>Uppdatera schemat
Stream Analytics härleder modellschemat data baserat på den första uppsättningen händelser i utdata. Senare, om det behövs uppdateras modellschemat som data för att hantera inkommande händelser som inte kanske passar i den ursprungliga scheman.

Den `SELECT *` fråga bör undvikas för att förhindra dynamisk schemauppdatering på rader. Förutom potentiella konsekvenser för prestanda, kan det också resultera i osäkerheten på om den tid det tar för resultat. De exakta fält som behöver som ska visas på Power BI-instrumentpanel måste väljas. Datavärdena bör dessutom vara kompatibel med datatypen.


Föregående/aktuell | Int64 | Sträng | DateTime | Double-värde
-----------------|-------|--------|----------|-------
Int64 | Int64 | Sträng | Sträng | Double-värde
Double-värde | Double-värde | Sträng | Sträng | Double-värde
Sträng | Sträng | Sträng | Sträng |  | Sträng | 
DateTime | Sträng | Sträng |  DateTime | Sträng


### <a name="renew-power-bi-authorization"></a>Förnya auktoriseringen för Power BI
Om lösenordet för Power BI-kontot ändras när ditt Stream Analytics-jobb skapades eller senast autentiserade, måste du autentiseras på nytt Stream Analytics. Om Multi-Factor Authentication (MFA) har konfigurerats på din Azure Active Directory (AAD)-klient, måste du också förnya auktoriseringen för Power BI varannan vecka. Ett symtom på det här problemet är ingen jobbutdata och en ”autentisera användaren error” i loggarna för åtgärden:

  ![Power BI-uppdatering, tokenfel](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

Stoppa körs jobbet för att lösa problemet och gå till Power BI-utdata.  Välj den **förnya auktoriseringen** länka och starta om jobbet från den **senast stoppad** att undvika dataförlust.

  ![Powerbi förnyar auktorisering](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Table Storage
[Azure tabellagring](../storage/common/storage-introduction.md) erbjuder hög tillgänglighet och mycket skalbar lagring, så att ett program kan anpassas automatiskt efter användarens behov. Table storage är Microsofts NoSQL nyckel-och attributdatabas, vilket kan utnyttja för strukturerade data med färre begränsningar i schemat. Azure Table storage kan användas för att lagra data för persistence och effektiv Filhämtning.

I tabellen nedan visas vilka egenskapsnamn och deras beskrivning för att skapa ett tabellutdata.

| Egenskapsnamn | beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera utdata till table storage. |
| Lagringskonto |Namnet på det lagringskonto där du skickar dina utdata. |
| Lagringskontonyckel |Åtkomstnyckeln som är associerade med lagringskontot. |
| Tabellnamn |Namnet på tabellen. Tabellen skapas om det inte finns. |
| Partitionsnyckeln |Namnet på utdatakolumnen innehåller Partitionsnyckeln. Partitionsnyckeln är en unik identifierare för partitionen i en given tabell som utgör den första delen av en entitets primärnyckel. Det är ett strängvärde som kan vara upp till 1 KB stora. |
| Radnyckel |Namnet på utdatakolumnen innehåller Radnyckeln. Radnyckeln är en unik identifierare för en entitet i en given partition. Den utgör den andra delen av en entitets primärnyckel. Radnyckeln är ett strängvärde som kan vara upp till 1 KB stora. |
| Batchstorlek |Antalet poster för en batchåtgärd. Standardvärdet (100) är tillräckliga för de flesta jobb. Referera till den [tabell batchåtgärd spec](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) för mer information om hur du ändrar den här inställningen. |
 
## <a name="service-bus-queues"></a>Service Bus-köer
[Service Bus-köer](https://msdn.microsoft.com/library/azure/hh367516.aspx) erbjuder en First In, First Out (FIFO) meddelandeleverans till en eller flera konkurrerande konsumenter. Vanligtvis förväntas meddelanden tas emot och bearbetas av mottagarna i den ordning som de har lagts till i kön och varje meddelande tas emot och bearbetas av endast en meddelandekonsument.

I tabellen nedan visas vilka egenskapsnamn och deras beskrivning för att skapa en kö-utdata.

| Egenskapsnamn | beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera utdata till det här Service Bus-kö. |
| Service Bus-namnområde |Ett Service Bus-namnområde är en behållare för en uppsättning meddelandeentiteter. |
| Könamn |Namnet på Service Bus-kö. |
| Köprincipnamn |När du skapar en kö kan skapa du också principer för delad åtkomst på fliken Konfigurera för kön. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. |
| Köprincipnyckel |Den delade åtkomstnyckeln som används för att autentisera åtkomst till Service Bus-namnområdet |
| Händelseserialiseringsformat |Serialiseringsformat för utdata.  JSON-, CSV- och Avro stöds. |
| Kodning |CSV och JSON är UTF-8 Kodningsformatet som endast stöds just nu |
| Avgränsare |Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering av data i CSV-format. Värden som stöds är kommatecken, semikolon, utrymme, fliken och lodrät stapel. |
| Format |Gäller endast för JSON-typen. Radseparering innebär att utdata formateras genom att låta varje JSON-objekt avgränsas med en ny rad. Matrisen anger att utdata formateras som en matris av JSON-objekt. |

Antalet partitioner är [baserat på Service Bus-SKU och storleken](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckeln är ett heltalsvärde som unikt för varje partition.

## <a name="service-bus-topics"></a>Avsnitt om Service Bus
När Service Bus-köer ger en en-till-en kommunikationsmetod från sändare till mottagare, [Service Bus-ämnen](https://msdn.microsoft.com/library/azure/hh367516.aspx) en en-till-många-formen av kommunikation.

I tabellen nedan visas vilka egenskapsnamn och deras beskrivning för att skapa ett tabellutdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera utdata till det här Service Bus-ämne. |
| Service Bus-namnområde |Ett Service Bus-namnområde är en behållare för en uppsättning meddelandeentiteter. När du har skapat en ny Händelsehubb, skapade du även en Service Bus-namnområde |
| Ämnesnamn |Ämnen är meddelandeentiteter, liknande event hubs och köer. De har skapats för att samla in händelseströmmar från ett antal olika enheter och tjänster. När ett ämne har skapats, ges även ett specifikt namn. Meddelanden som skickats till ett ämne är inte tillgängligt om inte en prenumeration skapas, så se till att det finns en eller flera prenumerationer under avsnittet |
| Ämnesprincipnamn |När du skapar ett ämne kan skapa du också principer för delad åtkomst på fliken avsnittet Konfigurera. Varje princip för delad åtkomst har namn, behörigheter som du ställa in och åtkomstnycklar |
| Ämnesprincipnyckel |Den delade åtkomstnyckeln som används för att autentisera åtkomst till Service Bus-namnområdet |
| Händelseserialiseringsformat |Serialiseringsformat för utdata.  JSON-, CSV- och Avro stöds. |
| Kodning |Om du använder CSV eller JSON-format, måste en kodning anges. UTF-8 är Kodningsformatet som endast stöds just nu |
| Avgränsare |Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering av data i CSV-format. Värden som stöds är kommatecken, semikolon, utrymme, fliken och lodrät stapel. |

Antalet partitioner är [baserat på Service Bus-SKU och storleken](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckeln är ett heltalsvärde som unikt för varje partition.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) är en globalt distribuerad databas tjänst som erbjuder obegränsad elastisk skalning runt hela världen, frågekörning och den automatiska indexeringen över schemaoberoende datamodeller, garanterad låg latens som branschledande omfattande serviceavtal. Mer information om alternativ för Cosmos DB-samling för Stream Analytics, som avser den [Stream Analytics med Cosmos DB som utdata](stream-analytics-documentdb-output.md) artikeln.

Azure Cosmos DB-utdata från Stream Analytics är för närvarande inte tillgänglig i regionerna för Azure Tyskland (T-Systems International) och Azure Kina (21Vianet).

> [!Note]
> För närvarande stöder Azure Stream Analytics endast anslutning till CosmosDB med hjälp av **SQL API**.
> Andra Azure Cosmos DB API: er stöds inte ännu. Om du punkt Azure Stream Analytics till Azure Cosmos DB-konton som skapats med andra API: er, kanske data inte korrekt lagras. 

I följande tabell beskrivs egenskaperna för att skapa ett Azure Cosmos DB-utdata.
| Egenskapsnamn | beskrivning |
| --- | --- |
| Utdataalias | Ett alias till se detta utdata i din Stream Analytics-fråga. |
| Kanalmottagare | Cosmos DB |
| Importalternativ | Välj antingen ”Välj Cosmos DB från din prenumeration”, eller till ”ange Cosmos DB-inställningar manuellt”.
| Konto-ID | Namn eller slutpunkten URI: N för Cosmos DB-kontot. |
| Kontonyckel | Den delade åtkomstnyckeln för Cosmos DB-kontot. |
| Databas | Namnet på Cosmos DB-databasen. |
| Samlingsnamnsmönster | Namnet på samlingen eller deras mönster för samlingar som ska användas. <br/>Samlingsnamnsformatet kan konstrueras med valfritt {partition}-token, där partitionerna börjar från 0. Två exempel:  <br/>1. _MyCollection_ – en samling som heter ”MyCollection” måste finnas.  <br/>2. _MyCollection {partition}_ – baserat på partitioneringskolumnen. <br/>Partitionering kolumnen samlingarna måste finnas – ”MyCollection0”, ”MyCollection1”, ”MyCollection2” och så vidare. |
| Partitionsnyckel | Valfri. Detta behövs bara om du använder en token för {partition} i din samlingsnamnsmönstret.<br/> Partitionsnyckeln är namnet på fältet i utdatahändelserna används för att ange nyckel för att partionera utdata över samlingarna.<br/> Alla godtyckliga utdatakolumnen kan användas för enskild samling utdata. Till exempel PartitionId. |
| Dokument-id |Valfri. Namnet på fältet i utdatahändelserna används för att ange den primära nyckeln operations baseras på vilken insert eller update.  

## <a name="azure-functions"></a>Azure Functions
Azure Functions är en serverlös beräkningstjänst som gör det möjligt köra kod på begäran utan att behöva installera eller hantera infrastruktur. Du kan implementera kod som utlöses av händelser i Azure eller tjänster från tredje part.  Azure Functions kapacitet att reagera på utlösare gör det ett naturligt utdatamål för ett Azure Stream Analytics. Den här utdataadapter tillåter användare att ansluta Stream Analytics till Azure Functions och köra ett skript eller kod som svar på en rad olika händelser.

Azure Functions-utdata från Stream Analytics är för närvarande inte tillgänglig i regionerna för Azure Tyskland (T-Systems International) och Azure Kina (21Vianet).

Azure Stream Analytics anropar Azure Functions via HTTP-utlösare. Det nya kortet Azure Funktionsutdata är tillgänglig med konfigurerbara egenskaper som är följande:

| Egenskapsnamn | beskrivning |
| --- | --- |
| Funktionsapp |Namnet på din Azure Functions-App |
| Funktion |Namnet på funktionen i Azure Functions-App |
| Nyckel |Om du vill använda en Azure-funktion från en annan prenumeration kan göra du det genom att ange nyckel för att få åtkomst till din funktion |
| Max batchstorlek |Den här egenskapen kan användas för att ange den maximala storleken för varje utdatabatch som skickas till din Azure-funktion. Det här värdet är som standard, 256 KB |
| Max batchantal  |Som namnet indikerar, kan du ange det maximala antalet händelser i varje batch som skickas till Azure Functions i den här egenskapen. Högsta batch-antal standardvärdet är 100 |

När Azure Stream Analytics tar emot 413 (http-begäran enhet är stor)-undantag från Azure-funktion, minskar storleken på batcharna som skickas till Azure Functions. Använd det här undantaget för att se till att Azure Stream Analytics inte skickar för stora batchar i Azure function-koden. Kontrollera också att de högsta batch-antal och storlek värden som används i funktionen stämmer överens med de värden som anges i Stream Analytics-portalen. 

I en situation där det finns ingen händelse som hamnar under en tidsperiod, inga utdata skapas. Därför anropas computeResult funktionen inte. Det här beteendet är konsekvent med de inbyggda mängdfunktionerna.

## <a name="partitioning"></a>Partitionering

I följande tabell sammanfattas partition-stöd och antalet skrivare för utdata för varje Utdatatyp av:

| Utdatatyp | Partitionering | Partitionsnyckeln  | Antalet utdata-skrivare | 
| --- | --- | --- | --- |
| Azure Data Lake Store | Ja | Använd {date} och {time}-token i prefixmönster för sögväg. Välj datumformat, till exempel ÅÅÅÅ/MM/DD, DD/MM/ÅÅÅÅ, MM-DD-ÅÅÅÅ. HH används för tidsformatet. | Följer inkommande partitionering för [helt kan frågor](stream-analytics-scale-jobs.md). | 
| Azure SQL Database | Ja | Baserat på PARTITION BY-sats i frågan | Följer inkommande partitionering för [helt kan frågor](stream-analytics-scale-jobs.md). | 
| Azure Blob Storage | Ja | Använd {date} och {time}-token från dina event fält i mönstret för sökvägen. Välj datumformat, till exempel ÅÅÅÅ/MM/DD, DD/MM/ÅÅÅÅ, MM-DD-ÅÅÅÅ. HH används för tidsformatet. Som en del av den [förhandsversion](https://aka.ms/ASApreview1), blob-utdata kan partitioneras av en enda anpassad händelseattribut {fieldname} eller {datetime:\<specificerare >}. | Följer inkommande partitionering för [helt kan frågor](stream-analytics-scale-jobs.md). | 
| Azure händelsehubb | Ja | Ja | Varierar beroende på partitionen justering.</br> När utdata utdata Event Hub partitionsnyckel justeras lika med överordnad (föregående) frågesteg, antalet skrivare är samma antalet händelsehubbspartitioner. Varje skrivare använder Eventhub's [EventHubSender klass](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) att skicka händelser till en specifik partition. </br> När utdata Event Hub partitionsnyckel inte är justerad med överordnad (föregående) frågesteg, antalet skrivare är samma som antalet partitioner i det föregående steget. Varje skrivare använder EventHubClient [SendBatchAsync klass](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) att skicka händelser till utdata-partitionerna. |
| Power BI | Nej | Ingen | Ej tillämpligt. | 
| Azure Table Storage | Ja | Alla utdatakolumn.  | Följer inkommande partitionering för [fullständigt parallelliseras frågor](stream-analytics-scale-jobs.md). | 
| Azure Service Bus-ämne | Ja | Valt automatiskt. Antalet partitioner är baserad på den [Service Bus-SKU och storlek](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckeln är ett heltalsvärde som unikt för varje partition.| Samma som antalet partitioner i avsnittet om utdata.  |
| Azure Service Bus-kö | Ja | Valt automatiskt. Antalet partitioner är baserad på den [Service Bus-SKU och storlek](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckeln är ett heltalsvärde som unikt för varje partition.| Samma som antalet partitioner i den utgående kön. |
| Azure Cosmos DB | Ja | Använd {partition}-token i samlingsnamnsmönstret. värdet för {partition} baseras på PARTITION BY-sats i frågan. | Följer inkommande partitionering för [fullständigt parallelliseras frågor](stream-analytics-scale-jobs.md). |
| Azure Functions | Nej | Ingen | Ej tillämpligt. | 

## <a name="output-batch-size"></a>Batchstorlek för utdata
Azure Stream Analytics använder variabel storlek batchar att bearbeta händelser och skriva till utdata. Vanligtvis Stream Analytics-motor kan inte att skriva ett meddelande i taget och använder batchar för effektivitet. När både inkommande och utgående händelser frekvensen är hög använder större batchar. När den utgående hastigheten är låg, använder mindre Batcher för att hålla det med låg latens. 

I följande tabell beskrivs några av överväganden för att mata ut batchbearbetning:

| Utdatatyp | Maxstorlek på meddelande | Optimering av batch-storlek |
| :--- | :--- | :--- | 
| Azure Data Lake Store | Se [begränsar Data Lake Storage](../azure-subscription-service-limits.md#data-lake-store-limits) | Upp till 4 MB per skrivåtgärd |
| Azure SQL Database | 10 000 maximalt antal rader per enskild bulk insert</br>100 min rader per enskild massinfogning </br>Se även [begränsar Azure SQL](../sql-database/sql-database-resource-limits.md) |  Varje batch som ursprungligen bulk infogas med Max batchstorlek och kan delas upp batch till hälften (fram till Min batchstorlek) baserat på återförsökbart fel från SQL. |
| Azure Blob Storage | Se [begränsar Azure Storage](../azure-subscription-service-limits.md#storage-limits) | Maxstorleken för Blob-block är 4 MB</br>Maximal blobbantal bock är 50000 |
| Azure händelsehubb   | 256 KB per meddelande </br>Se även [Event Hubs begränsar](../event-hubs/event-hubs-quotas.md) |    När o partitionering inte justera är varje händelse späckad individuellt i en EventData och skickas i en batch med upp till den maximala meddelandestorleken (1 MB för Premium). </br></br>  När in-och utdata partitionering är justerade, allt i ett enda EventData upp till max meddelandestorlek flera händelser och skickas.    |
| Power BI | Se [begränsar Power BI Rest API](https://msdn.microsoft.com/library/dn950053.aspx) |
| Azure Table Storage | Se [begränsar Azure Storage](../azure-subscription-service-limits.md#storage-limits) | Standardvärdet är 100 entiteter per enskild transaktion och kan konfigureras till ett mindre värde efter behov. |
| Azure Service Bus-kö   | 256 KB per meddelande</br> Se även [begränsar Service Bus](../service-bus-messaging/service-bus-quotas.md) | Enskild händelse per meddelande |
| Azure Service Bus-ämne | 256 KB per meddelande</br> Se även [begränsar Service Bus](../service-bus-messaging/service-bus-quotas.md) | Enskild händelse per meddelande |
| Azure Cosmos DB   | Se [Azure Cosmos DB begränsar](../azure-subscription-service-limits.md#azure-cosmos-db-limits) | Batch storlek och frekvens är justerade dynamiskt utifrån CosmosDB-svar. </br> Inga förinställt begränsningar från Stream Analytics. |
| Azure Functions   | | Batch standardstorleken är 256 KB. </br> Standardvärdet för händelseantal per batch är 100. </br> Batchstorlek är konfigurerbar och kan ökas eller minskas i Stream Analytics [Utdataalternativ](#azure-functions). 

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]

> [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

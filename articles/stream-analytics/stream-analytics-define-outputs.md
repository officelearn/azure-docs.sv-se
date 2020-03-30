---
title: Förstå utdata från Azure Stream Analytics
description: I den här artikeln beskrivs alternativ för datautdata som är tillgängliga i Azure Stream Analytics, inklusive Power BI för analysresultat.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: e0b4bcac8494f136dde21b03422e12b72cecb8f3
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366444"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Förstå utdata från Azure Stream Analytics

I den här artikeln beskrivs de typer av utdata som är tillgängliga för ett Azure Stream Analytics-jobb. Med utdata kan du lagra och spara resultatet av Stream Analytics-jobbet. Genom att använda utdata kan du göra ytterligare affärsanalyser och datalagring av dina data.

När du utformar din Stream Analytics-fråga läser du namnet på utdata med hjälp av [INTO-satsen](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Du kan använda en enda utdata per jobb eller flera utdata per direktuppspelningsjobb (om du behöver dem) genom att tillhandahålla flera INTO-satser i frågan.

Om du vill skapa, redigera och testa Stream Analytics-jobbutdata kan du använda [Azure-portalen,](stream-analytics-quick-create-portal.md#configure-job-output) [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)och [Visual Studio](stream-analytics-quick-create-vs.md).

Vissa utdatatyper stöder [partitionering](#partitioning). [Utdatabatchstorlekar varierar](#output-batch-size) för att optimera dataflödet.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

Stream Analytics stöder [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md). Azure Data Lake Storage är en företagsomfattande, hyperskala lagringsplats för stordata analytiska arbetsbelastningar. Du kan använda DataSjölagring för att lagra data av valfri storlek, typ och inmatningshastighet för drift- och undersökningsanalys. Stream Analytics måste ha behörighet att komma åt Data Lake Storage.

Azure Data Lake Storage-utdata från Stream Analytics är för närvarande inte tillgängligt i azure China 21Vianet och Azure Germany (T-Systems International) regioner.

I följande tabell visas egenskapsnamn och beskrivningar för att konfigurera utdata från Data Lake Storage Gen 1.   

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias | Ett eget namn som används i frågor för att dirigera frågeutdata till DataSjö store. |
| Prenumeration | Prenumerationen som innehåller ditt Azure Data Lake Storage-konto. |
| Kontonamn | Namnet på kontot Data Lake Store där du skickar utdata. Du får en listruta med DataSjölagringskonton som är tillgängliga i din prenumeration. |
| Mönster för banprefix | Filsökvägen som används för att skriva dina filer i det angivna DataSjölagringskontot. Du kan ange en eller flera instanser av variablerna {date} och {time}:<br /><ul><li>Exempel 1: mapp1/loggar/{datum}/{tid}</li><li>Exempel 2: mapp1/loggar/{datum}</li></ul><br />Tidsstämpeln för den skapade mappstrukturen följer UTC och inte lokal tid.<br /><br />Om filsökvägsmönstret inte innehåller ett avslutande snedstreck (/) behandlas det sista mönstret i filsökvägen som ett filnamnsprefix. <br /><br />Nya filer skapas under dessa omständigheter:<ul><li>Ändring i utdataschema</li><li>Extern eller intern omstart av ett jobb</li></ul> |
| Datumformat | Valfri. Om datumtoken används i prefixsökvägen kan du välja det datumformat som filerna är ordnade i. Exempel: YYYY/MM/DD |
|Tidsformat | Valfri. Om tidstoken används i prefixsökvägen anger du det tidsformat som filerna är ordnade i. För närvarande är det enda värde som stöds HH. |
| Händelseserialiseringsformat | Serieringsformatet för utdata. JSON, CSV och Avro stöds.|
| Kodning | Om du använder CSV- eller JSON-format måste en kodning anges. UTF-8 är det enda kodningsformat som stöds just nu.|
| Avgränsare | Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal vanliga avgränsare för serialisering av CSV-data. Värden som stöds är komma, semikolon, blanksteg, flik och lodrät stapel.|
| Format | Gäller endast för JSON-serialisering. **Radavskilt** anger att utdata formateras genom att varje JSON-objekt avgränsas med en ny rad. Om du väljer **Radavskiljad**läses JSON ett objekt i taget. Hela innehållet i sig skulle inte vara en giltig JSON.  **Matrisen** anger att utdata är formaterad som en matris med JSON-objekt. Den här matrisen stängs bara när jobbet stoppas eller Stream Analytics har gått vidare till nästa tidsfönster. I allmänhet är det bättre att använda radavgränsade JSON, eftersom det inte kräver någon särskild hantering medan utdatafilen fortfarande skrivs till.|
| Autentiseringsläge | Du kan auktorisera åtkomst till ditt DataSjölagringskonto med [hanterad identitet](stream-analytics-managed-identities-adls.md) eller användartoken. När du har beviljat åtkomst kan du återkalla åtkomsten genom att ändra lösenordet för användarkontot, ta bort datasjölagringsutdata för det här jobbet eller ta bort Stream Analytics-jobbet. |

## <a name="sql-database"></a>SQL Database

Du kan använda [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) som utdata för data som är relationella till sin natur eller för program som är beroende av innehåll som finns i en relationsdatabas. Stream Analytics-jobb skriv till en befintlig tabell i SQL Database. Tabellschemat måste exakt matcha fälten och deras typer i jobbets utdata. Du kan också ange [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) som en utdata via SQL Database-utdataalternativet. Mer information om olika sätt att förbättra skrivflödet finns i [avsnittet Stream Analytics med Azure SQL Database som utdataartikel.](stream-analytics-sql-output-perf.md)

Du kan också använda [Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) som utdata. Du måste [konfigurera offentlig slutpunkt i Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) och sedan konfigurera följande inställningar manuellt i Azure Stream Analytics. Azure virtuell dator som kör SQL Server med en bifogad databas stöds också genom att manuellt konfigurera inställningarna nedan.

I följande tabell visas egenskapsnamnen och deras beskrivning för att skapa ett SQL Database-utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera frågeutdata till den här databasen. |
| Databas | Namnet på databasen där du skickar utdata. |
| servernamn | Servernamnet för SQL Database. För Azure SQL Database Managed Instance måste den ange port 3342. Exempel *på exempel, sampleserver.public.database.windows.net,3342* |
| Användarnamn | Användarnamnet som har skrivåtkomst till databasen. Stream Analytics stöder endast SQL-autentisering. |
| lösenord | Lösenordet för att ansluta till databasen. |
| Tabell | Tabellnamnet där utdata skrivs. Tabellnamnet är skiftlägeskänsligt. Schemat för den här tabellen bör exakt matcha antalet fält och deras typer som projektutdata genererar. |
|Ärva partitionsschema| Ett alternativ för att ärva partitioneringsschemat för föregående frågesteg, för att aktivera helt parallell topologi med flera författare till tabellen. Mer information finns i [Azure Stream Analytics-utdata till Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Maximalt antal batchar| Den rekommenderade övre gränsen för antalet poster som skickas med varje bulkinfogningstransaktion.|

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Blob-lagring och Azure Data Lake Gen2

Data Lake Storage Gen2 gör Azure Storage till grunden för att bygga företagsdatasjöar på Azure. Designad från början för att betjäna flera petabyte information samtidigt upprätthålla hundratals gigabits av dataflöde, datasjölagring Gen2 kan du enkelt hantera stora mängder data. En grundläggande del av Data Lake Storage Gen2 är tillägget av ett hierarkiskt namnområde till Blob-lagring.

Azure Blob storage erbjuder en kostnadseffektiv och skalbar lösning för lagring av stora mängder ostrukturerade data i molnet. En introduktion om Blob-lagring och dess användning finns i [Ladda upp, hämta och lista blobbar med Azure-portalen](../storage/blobs/storage-quickstart-blobs-portal.md).

I följande tabell visas egenskapsnamnen och beskrivningarna för att skapa en blob- eller ADLS Gen2-utdata.

| Egenskapsnamn       | Beskrivning                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Utdataalias        | Ett eget namn som används i frågor för att dirigera frågeutdata till den här blob-lagringen. |
| Lagringskonto     | Namnet på det lagringskonto där du skickar utdata.               |
| Nyckeln för lagringskonto | Den hemliga nyckeln som är associerad med lagringskontot.                              |
| Behållare för lagring   | En logisk gruppering för blobbar som lagras i Azure Blob-tjänsten. När du överför en blob till Blob-tjänsten måste du ange en behållare för den blobben. |
| Sökvägsmönster | Valfri. Filsökvägsmönstret som används för att skriva blobbar i den angivna behållaren. <br /><br /> I banmönstret kan du välja att använda en eller flera förekomster av datum- och tidsvariablerna för att ange hur ofta blobbar skrivs: <br /> {datum}, {tid} <br /><br />Du kan använda anpassad blob-partitionering för att ange ett anpassat {field}-namn från händelsedata till partitionsblobar. Fältnamnet är alfanumeriskt och kan innehålla blanksteg, bindestreck och understreck. Begränsningar för anpassade fält omfattar följande: <ul><li>Fältnamn är inte skiftlägeskänsliga. Tjänsten kan till exempel inte skilja mellan kolumnen "ID" och kolumn "id".</li><li>Kapslade fält är inte tillåtna. Använd i stället ett alias i jobbfrågan för att "förenkla" fältet.</li><li>Uttryck kan inte användas som fältnamn.</li></ul> <br />Med den här funktionen kan du använda anpassade konfigurationer för datum-/tidsformatspecificerare i sökvägen. Anpassade datum- och tidsformat måste anges en i taget, omgiven av nyckelordet {datetime:\<specifier>} . Tillåtna ingångar \<för specificerare> är yyyy, MM, M, dd, d, HH, H, mm, m, ss eller s. Nyckelordet {datetime:\<specifier>} kan användas flera gånger i sökvägen för att skapa anpassade datum-/tidskonfigurationer. <br /><br />Exempel: <ul><li>Exempel 1: kluster1/loggar/{datum}/{tid}</li><li>Exempel 2: kluster1/loggar/{datum}</li><li>Exempel 3: cluster1/{client_id}/{date}/{time}</li><li>Exempel 4: cluster1/{datetime:ss}/{myField} där frågan är: SELECT data.myField AS myField FROM Input;</li><li>Exempel 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />Tidsstämpeln för den skapade mappstrukturen följer UTC och inte lokal tid.<br /><br />Filnamnstillägg använder följande konvention: <br /><br />{Path Prefix Pattern}/schemaHashcode_Guid_Number.extension<br /><br />Exempel på utdatafiler:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Mer information om den här funktionen finns i [Azure Stream Analytics anpassade blob-utdatapartitionering](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Datumformat | Valfri. Om datumtoken används i prefixsökvägen kan du välja det datumformat som filerna är ordnade i. Exempel: YYYY/MM/DD |
| Tidsformat | Valfri. Om tidstoken används i prefixsökvägen anger du det tidsformat som filerna är ordnade i. För närvarande är det enda värde som stöds HH. |
| Händelseserialiseringsformat | Serialiseringsformat för utdata. JSON, CSV, Avro och Parkett stöds. |
|Minsta rader (endast parkett)|Antalet minsta rader per batch. För Parkett, kommer varje parti att skapa en ny fil. Det aktuella standardvärdet är 2 000 rader och det tillåtna maxvärdet är 10 000 rader.|
|Maximal tid (endast parkett)|Den maximala väntetiden per batch. Efter denna tid skrivs batchen till utdata även om kravet på minsta rader inte uppfylls. Det aktuella standardvärdet är 1 minut och det tillåtna maxvärdet är 2 timmar. Om blob-utdata har banmönsterfrekvens kan väntetiden inte vara högre än partitionstidintervallet.|
| Kodning    | Om du använder CSV- eller JSON-format måste en kodning anges. UTF-8 är det enda kodningsformat som stöds just nu. |
| Avgränsare   | Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal vanliga avgränsare för serialisering av CSV-data. Värden som stöds är komma, semikolon, blanksteg, flik och lodrät stapel. |
| Format      | Gäller endast för JSON-serialisering. **Radavskilt** anger att utdata formateras genom att varje JSON-objekt avgränsas med en ny rad. Om du väljer **Radavskiljad**läses JSON ett objekt i taget. Hela innehållet i sig skulle inte vara en giltig JSON. **Matrisen** anger att utdata är formaterad som en matris med JSON-objekt. Den här matrisen stängs bara när jobbet stoppas eller Stream Analytics har gått vidare till nästa tidsfönster. I allmänhet är det bättre att använda radavgränsade JSON, eftersom det inte kräver någon särskild hantering medan utdatafilen fortfarande skrivs till. |

När du använder Blob-lagring som utdata skapas en ny fil i blobben i följande fall:

* Om filen överskrider det maximala antalet tillåtna block (för närvarande 50 000). Du kan nå det högsta tillåtna antalet block utan att nå den högsta tillåtna blob-storleken. Om utdatahastigheten till exempel är hög kan du se fler byte per block och filstorleken är större. Om utdatahastigheten är låg har varje block mindre data och filstorleken är mindre.
* Om det finns en schemaändring i utdata och utdataformatet kräver fast schema (CSV och Avro).
* Om ett jobb startas om, antingen externt av en användare som stoppar det och startar det, eller internt för systemunderhåll eller felåterställning.
* Om frågan är helt partitionerad och en ny fil skapas för varje utdatapartition.
* Om användaren tar bort en fil eller en behållare med lagringskontot.
* Om utdata är tidspartitionerat med hjälp av sökvägsprefixmönstret och en ny blob används när frågan flyttas till nästa timme.
* Om utdata partitioneras av ett anpassat fält och en ny blob skapas per partitionsnyckel om den inte finns.
* Om utdata partitioneras av ett anpassat fält där partitionsnyckeln kardinalitet överstiger 8.000 och en ny blob skapas per partitionsnyckel.

## <a name="event-hubs"></a>Händelsehubbar

[Azure Event Hubs-tjänsten](https://azure.microsoft.com/services/event-hubs/) är en mycket skalbar publicerings-prenumerera händelse ingestor. Det kan samla in miljontals händelser per sekund. En användning av en händelsehubb som utdata är när utdata från ett Stream Analytics-jobb blir indata för ett annat direktuppspelningsjobb. Information om den maximala meddelandestorleken och optimeringen för batchstorlek finns i avsnittet [utdatabatchstorlek.](#output-batch-size)

Du behöver några parametrar för att konfigurera dataströmmar från händelsehubbar som utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias | Ett eget namn som används i frågor för att dirigera frågeutdata till den här händelsehubben. |
| Namnområde för händelsehubb | En behållare för en uppsättning meddelandeentiteter. När du skapade en ny händelsehubb har du också skapat ett namnområde för händelsehubben. |
| Namn på händelsehubb | Namnet på händelsehubbens utdata. |
| Principnamn för händelsenav | Principen för delad åtkomst, som du kan skapa på fliken **Konfigurera** händelsehubben. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomstnycklar. |
| Principnyckel för händelsenav | Den delade åtkomstnyckeln som används för att autentisera åtkomst till namnområdet för händelsehubben. |
| Kolumnen Partitionsnyckel | Valfri. En kolumn som innehåller partitionsnyckeln för händelsehubbutdata. |
| Händelseserialiseringsformat | Serieringsformatet för utdata. JSON, CSV och Avro stöds. |
| Kodning | För CSV och JSON är UTF-8 det enda kodningsformat som stöds just nu. |
| Avgränsare | Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal vanliga avgränsare för serialisering av data i CSV-format. Värden som stöds är komma, semikolon, blanksteg, flik och lodrät stapel. |
| Format | Gäller endast för JSON-serialisering. **Radavskilt** anger att utdata formateras genom att varje JSON-objekt avgränsas med en ny rad. Om du väljer **Radavskiljad**läses JSON ett objekt i taget. Hela innehållet i sig skulle inte vara en giltig JSON. **Matrisen** anger att utdata är formaterad som en matris med JSON-objekt.  |
| Egenskapskolumner | Valfri. Kommaavgränsade kolumner som måste kopplas som användaregenskaper för det utgående meddelandet i stället för nyttolasten. Mer information om den här funktionen finns i avsnittet [Anpassade metadataegenskaper för utdata](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

Du kan använda [Power BI](https://powerbi.microsoft.com/) som utdata för ett Stream Analytics-jobb för att ge en omfattande visualiseringsupplevelse av analysresultat. Du kan använda den här funktionen för operativa instrumentpaneler, rapportgenerering och måttdriven rapportering.

Power BI-utdata från Stream Analytics är för närvarande inte tillgängligt i azure China 21Vianet och Azure Germany (T-Systems International) regioner.

I följande tabell visas egenskapsnamn och beskrivningar för att konfigurera Power BI-utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ange ett eget namn som används i frågor för att dirigera frågeutdata till den här Power BI-utdata. |
| Grupparbetsyta |Om du vill aktivera delning av data med andra Power BI-användare kan du välja grupper i ditt Power BI-konto eller välja **Min arbetsyta** om du inte vill skriva till en grupp. För att uppdatera en befintlig grupp måste Power BI-autentiseringen förnyas. |
| Namn på datauppsättning |Ange ett datauppsättningsnamn som du vill att Power BI-utdata ska användas. |
| Tabellnamn |Ange ett tabellnamn under datauppsättningen för Power BI-utdata. För närvarande kan Power BI-utdata från Stream Analytics-jobb bara ha en tabell i en datauppsättning. |
| Godkänna anslutning | Du måste auktorisera med Power BI för att konfigurera utdatainställningarna. När du har beviljat den här utdataåtkomsten till power bi-instrumentpanelen kan du återkalla åtkomsten genom att ändra lösenordet för användarkontot, ta bort jobbutdata eller ta bort Stream Analytics-jobbet. | 

En genomgång av att konfigurera en Power BI-utdata och instrumentpanel finns i azure [stream analytics- och Power BI-självstudien.](stream-analytics-power-bi-dashboard.md)

> [!NOTE]
> Skapa inte explicit datauppsättningen och tabellen på Power BI-instrumentpanelen. Datauppsättningen och tabellen fylls i automatiskt när jobbet startas och jobbet börjar pumpa utdata till Power BI. Om jobbfrågan inte genererar några resultat skapas inte datauppsättningen och tabellen. Om Power BI redan hade en datauppsättning och tabell med samma namn som det som anges i det här Stream Analytics-jobbet skrivs befintliga data över.
>

### <a name="create-a-schema"></a>Skapa ett schema
Azure Stream Analytics skapar en Power BI-datauppsättning och tabellschema för användaren om de inte redan finns. I alla andra fall uppdateras tabellen med nya värden. För närvarande kan bara en tabell finnas i en datauppsättning. 

Power BI använder principen för första in-, först-ut-lagringsprincipen (FIFO). Data samlas in i en tabell tills de når 200 000 rader.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Konvertera en datatyp från Stream Analytics till Power BI
Azure Stream Analytics uppdaterar datamodellen dynamiskt vid körning om utdataschemat ändras. Kolumnnamnsändringar, kolumntypändringar och tillägg eller borttagning av kolumner spåras alla.

Den här tabellen omfattar datatypskonverteringar från [Stream Analytics-datatyper](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) till [EDM-typer (Power](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)BI Entity Data Model), om det inte finns en Power BI-datauppsättning och tabell.

Från Stream Analytics | Till Power BI
-----|-----
bigint | Int64
nvarchar(max) | String
datetime | Datumtid
float | Double
Postmatris | Strängtyp, konstant värde "IRecord" eller "IArray"

### <a name="update-the-schema"></a>Uppdatera schemat
Stream Analytics härleder datamodellschemat baserat på den första uppsättningen händelser i utdata. Senare, om det behövs, uppdateras datamodellschemat för att hantera inkommande händelser som kanske inte passar in i det ursprungliga schemat.

Undvik `SELECT *` frågan för att förhindra dynamisk schemauppdatering över rader. Förutom potentiella prestandakonsekvenser kan det leda till osäkerhet om den tid det tar för resultaten. Välj de exakta fält som måste visas på Power BI-instrumentpanelen. Dessutom bör datavärdena vara kompatibla med den valda datatypen.


Föregående/aktuell | Int64 | String | Datumtid | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | String | String | String | String 
Datumtid | String | String |  Datumtid | String

## <a name="table-storage"></a>Table Storage

[Azure Table storage](../storage/common/storage-introduction.md) erbjuder mycket tillgänglig, massivt skalbar lagring, så att ett program automatiskt kan skalas för att möta användarnas efterfrågan. Tabelllagring är Microsofts NoSQL-nyckel/attributarkiv, som du kan använda för strukturerade data med färre begränsningar för schemat. Azure Table storage kan användas för att lagra data för beständighet och effektiv hämtning.

I följande tabell visas egenskapsnamnen och beskrivningarna för att skapa en tabellutdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera frågeutdata till den här tabelllagringen. |
| Lagringskonto |Namnet på det lagringskonto där du skickar utdata. |
| Nyckeln för lagringskonto |Åtkomstnyckeln som är associerad med lagringskontot. |
| Tabellnamn |Namnet på tabellen. Tabellen skapas om den inte finns. |
| Partitionsnyckeln |Namnet på utdatakolumnen som innehåller partitionsnyckeln. Partitionsnyckeln är en unik identifierare för partitionen i en tabell som utgör den första delen av en entitets primära nyckel. Det är ett strängvärde som kan vara upp till 1 KB i storlek. |
| Radtangent |Namnet på utdatakolumnen som innehåller radtangenten. Radnyckeln är en unik identifierare för en entitet i en partition. Den utgör den andra delen av en entitets primärnyckel. Radtangenten är ett strängvärde som kan vara upp till 1 KB i storlek. |
| Batchstorlek |Antalet poster för en batchoperation. Standardinställningen (100) är tillräcklig för de flesta jobb. Mer information om hur du ändrar den här inställningen finns i [specifikationen för tabellbatch-drift.](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._table_batch_operation) |

## <a name="service-bus-queues"></a>Service Bus-köer

[Service Bus köer](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) erbjuder en FIFO meddelande leverans till en eller flera konkurrerande konsumenter. Vanligtvis tas meddelanden emot och bearbetas av mottagarna i den tidsordning som de lades till i kön. Varje meddelande tas emot och bearbetas av endast en meddelandekonsument.

På [kompatibilitetsnivå 1.2](stream-analytics-compatibility-level.md)använder Azure Stream Analytics [AMQP-meddelandeprotokoll (Advanced Message Queueing Protocol)](../service-bus-messaging/service-bus-amqp-overview.md) för att skriva till Service Bus-köer och information. MED AMQP kan du skapa hybridprogram som inte är plattformarna och hybridprogram med ett öppet standardprotokoll.

I följande tabell visas egenskapsnamnen och beskrivningarna för att skapa en köutdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera frågeutdata till den här servicebusskön. |
| Service Bus namnområde |En behållare för en uppsättning meddelandeentiteter. |
| Könamn |Namnet på servicebusskön. |
| Namn på köprincip |När du skapar en kö kan du också skapa principer för delad åtkomst på fliken **Konfigurera** i kön. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomstnycklar. |
| Köprincipnyckel |Den delade åtkomstnyckeln som används för att autentisera åtkomsten till servicebussens namnområde. |
| Händelseserialiseringsformat |Serieringsformatet för utdata. JSON, CSV och Avro stöds. |
| Kodning |För CSV och JSON är UTF-8 det enda kodningsformat som stöds just nu. |
| Avgränsare |Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal vanliga avgränsare för serialisering av data i CSV-format. Värden som stöds är komma, semikolon, blanksteg, flik och lodrät stapel. |
| Format |Gäller endast för JSON-typ. **Radavskilt** anger att utdata formateras genom att varje JSON-objekt avgränsas med en ny rad. Om du väljer **Radavskiljad**läses JSON ett objekt i taget. Hela innehållet i sig skulle inte vara en giltig JSON. **Matrisen** anger att utdata är formaterad som en matris med JSON-objekt. |
| Egenskapskolumner | Valfri. Kommaavgränsade kolumner som måste kopplas som användaregenskaper för det utgående meddelandet i stället för nyttolasten. Mer information om den här funktionen finns i avsnittet [Anpassade metadataegenskaper för utdata](#custom-metadata-properties-for-output). |
| Kolumner för systemegenskap | Valfri. Nyckelvärdespar av systemegenskaper och motsvarande kolumnnamn som måste kopplas till det utgående meddelandet i stället för nyttolasten. Mer information om den här funktionen finns i avsnittet [Systemegenskaper för servicebusskö och ämnesutdata](#system-properties-for-service-bus-queue-and-topic-outputs)  |

Antalet partitioner [baseras på Service Bus SKU och storlek](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckel är ett unikt heltalsvärde för varje partition.

## <a name="service-bus-topics"></a>Avsnitt om Service Bus
Service Bus-köer ger en 1:1-kommunikationsmetod från avsändare till mottagare. [Service Bus ämnen](https://msdn.microsoft.com/library/azure/hh367516.aspx) ger en en-till-många form av kommunikation.

I följande tabell visas egenskapsnamnen och beskrivningarna för att skapa en servicebuss-utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera frågeutdata till det här Service Bus-avsnittet. |
| Service Bus namnområde |En behållare för en uppsättning meddelandeentiteter. När du skapade en ny händelsehubb har du också skapat ett servicebussnamnområde. |
| Ämnesnamn |Ämnen är meddelandeentiteter, liknande händelsehubbar och köer. De är utformade för att samla in händelseströmmar från enheter och tjänster. När ett ämne skapas får det också ett specifikt namn. De meddelanden som skickas till ett ämne är inte tillgängliga om inte en prenumeration skapas, så se till att det finns en eller flera prenumerationer under ämnet. |
| Namn på ämnesprincip |När du skapar ett Service Bus-ämne kan du också skapa principer för delad åtkomst på fliken **Konfigurera** i ämnet. Varje princip för delad åtkomst har ett namn, behörigheter som du anger och åtkomstnycklar. |
| Nyckel för ämnesprincip |Den delade åtkomstnyckeln som används för att autentisera åtkomsten till servicebussens namnområde. |
| Händelseserialiseringsformat |Serieringsformatet för utdata. JSON, CSV och Avro stöds. |
| Kodning |Om du använder CSV- eller JSON-format måste en kodning anges. UTF-8 är det enda kodningsformat som stöds just nu. |
| Avgränsare |Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal vanliga avgränsare för serialisering av data i CSV-format. Värden som stöds är komma, semikolon, blanksteg, flik och lodrät stapel. |
| Egenskapskolumner | Valfri. Kommaavgränsade kolumner som måste kopplas som användaregenskaper för det utgående meddelandet i stället för nyttolasten. Mer information om den här funktionen finns i avsnittet [Anpassade metadataegenskaper för utdata](#custom-metadata-properties-for-output). |
| Kolumner för systemegenskap | Valfri. Nyckelvärdespar av systemegenskaper och motsvarande kolumnnamn som måste kopplas till det utgående meddelandet i stället för nyttolasten. Mer information om den här funktionen finns i avsnittet [Systemegenskaper för servicebusskö och ämnesutdata](#system-properties-for-service-bus-queue-and-topic-outputs) |

Antalet partitioner [baseras på Service Bus SKU och storlek](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckeln är ett unikt heltalsvärde för varje partition.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) är en globalt distribuerad databastjänst som erbjuder obegränsad elastisk skala runt om i världen, omfattande fråga och automatisk indexering över schemaaoberoende datamodeller. Mer information om Azure Cosmos DB-behållaralternativ för Stream Analytics finns i [stream analytics med Azure Cosmos DB som utdataartikel.](stream-analytics-documentdb-output.md)

Azure Cosmos DB-utdata från Stream Analytics är för närvarande inte tillgängligt i azure China 21Vianet och Azure Germany (T-Systems International).

> [!Note]
> För närvarande stöder Azure Stream Analytics endast anslutning till Azure Cosmos DB med hjälp av SQL API.
> Andra Azure Cosmos DB API:er stöds ännu inte. Om du pekar Azure Stream Analytics på Azure Cosmos DB-konton som skapats med andra API:er kanske data inte lagras korrekt.

I följande tabell beskrivs egenskaperna för att skapa en Azure Cosmos DB-utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias | Ett alias som refererar till den här utdata i din Stream Analytics-fråga. |
| Kanalmottagare | Azure Cosmos DB. |
| Importalternativ | Välj antingen **Välj Cosmos DB från din prenumeration** eller Ange **Cosmos DB-inställningar manuellt**.
| Konto-ID | Namnet eller slutpunkten URI för Azure Cosmos DB-konto. |
| Kontonyckel | Nyckeln för delad åtkomst för Azure Cosmos DB-kontot. |
| Databas | Azure Cosmos DB-databasnamnet. |
| Containerns namn | Behållarnamnet som ska användas, som måste finnas i Cosmos DB. Exempel:  <br /><ul><li> _MyContainer_: En behållare med namnet "MyContainer" måste finnas.</li>|
| Dokument-ID |Valfri. Namnet på fältet i utdatahändelser som används för att ange den primära nyckel som infogas eller uppdateras.

## <a name="azure-functions"></a>Azure Functions
Azure Functions är en serverlös beräkningstjänst som du kan använda för att köra kod på begäran utan att uttryckligen behöva etablera eller hantera infrastruktur. Det låter dig implementera kod som utlöses av händelser som inträffar i Azure eller partnertjänster. Den här möjligheten för Azure Functions att svara på utlösare gör det till en naturlig utdata för Azure Stream Analytics. Med det här utdatakortet kan användare ansluta Stream Analytics till Azure Functions och köra ett skript eller en kod som svar på en mängd olika händelser.

Azure Functions-utdata från Stream Analytics är för närvarande inte tillgängligt i azure China 21Vianet och Azure Germany (T-Systems International).

Azure Stream Analytics anropar Azure-funktioner via HTTP-utlösare. Utdatakortet Azure Functions är tillgängligt med följande konfigurerbara egenskaper:

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Funktionsapp |Namnet på din Azure Functions-app. |
| Funktion |Namnet på funktionen i din Azure Functions-app. |
| Nyckel |Om du vill använda en Azure-funktion från en annan prenumeration kan du göra det genom att ange nyckeln för att komma åt din funktion. |
| Max batchstorlek |En egenskap som låter dig ange den maximala storleken för varje utdatabatch som skickas till din Azure-funktion. Indataenheten är i byte. Som standard är det här värdet 262 144 byte (256 KB). |
| Maximalt antal batchar  |En egenskap som låter dig ange det maximala antalet händelser i varje batch som skickas till Azure Functions. Standardvärdet är 100. |

Azure Stream Analytics förväntar sig HTTP-status 200 från functions-appen för batchar som har bearbetats.

När Azure Stream Analytics tar emot ett undantag på 413 ("http Request Entity Too Large") från en Azure-funktion minskar det storleken på de batchar som skickas till Azure-funktioner. I din Azure-funktionskod använder du det här undantaget för att se till att Azure Stream Analytics inte skickar överdimensionerade batchar. Kontrollera också att det maximala batchantalet och storleksvärdena som används i funktionen överensstämmer med de värden som anges i Stream Analytics-portalen.

> [!NOTE]
> Under testanslutningen skickar Stream Analytics en tom batch till Azure Functions för att testa om anslutningen mellan de två fungerar. Kontrollera att appen Functions hanterar tomma batchbegäranden för att se till att testanslutningen går över.

I en situation där det inte finns någon händelselandning i ett tidsfönster genereras ingen utdata. Därför anropas inte **funktionen computeResult.** Detta är förenligt med de inbyggda mängdfunktionerna i fönster.

## <a name="custom-metadata-properties-for-output"></a>Anpassade metadataegenskaper för utdata 

Du kan bifoga frågekolumner som användaregenskaper till dina utgående meddelanden. Dessa kolumner går inte in i nyttolasten. Egenskaperna finns i form av en ordlista i utdatameddelandet. *Nyckeln* är kolumnnamnet och *värdet* är kolumnvärdet i egenskapsordlistan. Alla Stream Analytics-datatyper stöds förutom Post och Array.  

Utdata som stöds: 
* Service Bus-kö 
* Service Bus-ämne 
* Händelsehubb 

I följande exempel lägger vi `DeviceId` `DeviceStatus` till de två fälten och metadata. 
* Fråga:`select *, DeviceId, DeviceStatus from iotHubInput`
* Utdatakonfiguration:`DeviceId,DeviceStatus`

![Egenskapskolumner](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

Följande skärmbild visar egenskaper för utdatameddelande som inspekterats i EventHub via [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

![Anpassade egenskaper för händelse](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>Systemegenskaper för servicebusskö och ämnesutdata 
Du kan bifoga frågekolumner som [systemegenskaper](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) till din utgående tjänstbusskö eller ämnesmeddelanden. Dessa kolumner går inte in i nyttolasten i stället motsvarande BrokeredMessage [system egenskap](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) fylls med frågekolumnen värden.
Dessa systemegenskaper stöds `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`- .
Strängvärden för dessa kolumner tolkas som motsvarande värdetyp för systemegenskap och eventuella tolkningsfel behandlas som datafel.
Det här fältet anges som ett JSON-objektformat. Information om detta format är följande -
* Omgiven av lockiga hängslen {}.
* Skrivet i nyckel/värdepar.
* Nycklar och värden måste vara strängar.
* Nyckeln är systemets egenskapsnamn och -värde är frågekolumnens namn.
* Nycklar och värden avgränsas med ett kolon.
* Varje nyckel/värdepar avgränsas med ett kommatecken.

Detta visar hur du använder den här egenskapen –

* Fråga:`select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Kolumner för systemegenskap:`{ "MessageId": "column1", "PartitionKey": "column2"}`

Detta anger `MessageId` på servicebusskömeddelanden `column1`med värden och PartitionKey `column2`är inställt med 's värden.

## <a name="partitioning"></a>Partitionering

I följande tabell sammanfattas partitionsstödet och antalet utdataskrivare för varje utdatatyp:

| Utdatatyp | Stöd för partitionering | Partitionsnyckeln  | Antal utdataförfattare |
| --- | --- | --- | --- |
| Azure Data Lake Store | Ja | Använd {date} och {time} token i sökvägsprefixmönstret. Välj datumformat, till exempel YYYY/DD, DD/MM/YYYY eller MM-DD-YYY. HH används för tidsformatet. | Följer indatapartitioneringen för [helt parallelliserbara frågor](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Ja, måste aktiveras. | Baserat på PARTITION BY-satsen i frågan. | När alternativet Ärva partitionering är aktiverat följer du indatapartitioneringen för [helt parallelliserbara frågor](stream-analytics-scale-jobs.md). Mer information om hur du uppnår bättre resultat för skrivdataflöde när du läser in data i Azure SQL Database finns i [Azure Stream Analytics-utdata till Azure SQL Database](stream-analytics-sql-output-perf.md). |
| Azure Blob Storage | Ja | Använd {date} och {time} token från dina händelsefält i sökvägsmönstret. Välj datumformat, till exempel YYYY/DD, DD/MM/YYYY eller MM-DD-YYY. HH används för tidsformatet. Blob-utdata kan partitioneras av ett enda anpassat händelseattribut\<{fieldname} eller {datetime: specifier>}. | Följer indatapartitioneringen för [helt parallelliserbara frågor](stream-analytics-scale-jobs.md). |
| Azure Event Hubs | Ja | Ja | Varierar beroende på partitionsjustering.<br /> När partitionsnyckeln för händelsehubbutdata är lika justerad med frågesteget uppströms (föregående) är antalet författare detsamma som antalet partitioner i händelsehubbutdata. Varje författare använder [klassen EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) för att skicka händelser till den specifika partitionen. <br /> När partitionsnyckeln för händelsehubbutdata inte är justerad med frågesteget uppströms (föregående) är antalet författare detsamma som antalet partitioner i det föregående steget. Varje författare använder [klassen SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) i **EventHubClient** för att skicka händelser till alla utdatapartitioner. |
| Power BI | Inga | Inget | Inte tillämpligt. |
| Azure Table Storage | Ja | Alla utdatakolumn.  | Följer indatapartitioneringen för [helt parallelliserade frågor](stream-analytics-scale-jobs.md). |
| Azure Service Bus-ämne | Ja | Väljs automatiskt. Antalet partitioner baseras på [Service Bus SKU och storlek](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckeln är ett unikt heltalsvärde för varje partition.| Samma som antalet partitioner i utdataämnet.  |
| Azure Service Bus-kö | Ja | Väljs automatiskt. Antalet partitioner baseras på [Service Bus SKU och storlek](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckeln är ett unikt heltalsvärde för varje partition.| Samma som antalet partitioner i utdatakön. |
| Azure Cosmos DB | Ja | Baserat på PARTITION BY-satsen i frågan. | Följer indatapartitioneringen för [helt parallelliserade frågor](stream-analytics-scale-jobs.md). |
| Azure Functions | Ja | Baserat på PARTITION BY-satsen i frågan. | Följer indatapartitioneringen för [helt parallelliserade frågor](stream-analytics-scale-jobs.md). |

Antalet utdataskrivare kan också `INTO <partition count>` kontrolleras med hjälp av (se INTO)-satsen i frågan, vilket kan vara till hjälp för att uppnå en önskad jobbtopologi. [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) Om utdatakortet inte är partitionerat kommer bristen på data i en indatapartition att orsaka en fördröjning upp till den sena ankomsttiden. I sådana fall slås utdata samman till en enda författare, vilket kan orsaka flaskhalsar i pipelinen. Mer information om principen om sen ankomst finns i [Azure Stream Analytics händelseorderöverväganden](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Batchstorlek för utdata
Azure Stream Analytics använder batchar med variabel storlek för att bearbeta händelser och skriva till utdata. Stream Analytics-motorn skriver vanligtvis inte ett meddelande i taget och använder batchar för effektivitet. När hastigheten för både inkommande och utgående händelser är hög använder Stream Analytics större batchar. När utgående hastigheten är låg används mindre batchar för att hålla svarstiden låg.

I följande tabell beskrivs några av övervägandena för batchbearbetning av utdata:

| Utdatatyp | Max meddelandestorlek | Optimering av batchstorlek |
| :--- | :--- | :--- |
| Azure Data Lake Store | Se [Begränsningar för lagring av datasjö](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-store-limits). | Använd upp till 4 MB per skrivåtgärd. |
| Azure SQL Database | Konfigurerbart med max batchantal. 10 000 max och 100 minsta rader per enskild bulkinsats som standard.<br />Se [Azure SQL-gränser](../sql-database/sql-database-resource-limits.md). |  Varje batch är ursprungligen bulk infogas med maximalt partiantal. Batchen delas på mitten (tills minsta batchantal) baseras på återförsöksbara fel från SQL. |
| Azure Blob Storage | Se [Azure Storage-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | Den maximala blobblockstorleken är 4 MB.<br />Det maximala blob bockantalet är 50 000. |
| Azure Event Hubs  | 256 KB eller 1 MB per meddelande. <br />Se [gränser för händelsehubbar](../event-hubs/event-hubs-quotas.md). |  När indata-/utdatapartitioneringen inte är justerad packas varje händelse individuellt in `EventData` och skickas i en batch med upp till den maximala meddelandestorleken. Detta händer också om [anpassade metadataegenskaper](#custom-metadata-properties-for-output) används. <br /><br />  När indata-/utdatapartitioneringen är justerad packas flera händelser i en enda `EventData` instans, upp till den maximala meddelandestorleken och skickas. |
| Power BI | Se [Api-gränser för Power BI-viloplats](https://msdn.microsoft.com/library/dn950053.aspx). |
| Azure Table Storage | Se [Azure Storage-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). | Standardvärdet är 100 enheter per enskild transaktion. Du kan konfigurera den till ett mindre värde efter behov. |
| Azure Service Bus-kö   | 256 KB per meddelande för standardnivå, 1 MB för Premium-nivå.<br /> Se [Service Bus gränser](../service-bus-messaging/service-bus-quotas.md). | Använd en enskild händelse per meddelande. |
| Azure Service Bus-ämne | 256 KB per meddelande för standardnivå, 1 MB för Premium-nivå.<br /> Se [Service Bus gränser](../service-bus-messaging/service-bus-quotas.md). | Använd en enskild händelse per meddelande. |
| Azure Cosmos DB   | Se [Azure Cosmos DB-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits). | Batchstorlek och skrivfrekvens justeras dynamiskt baserat på Azure Cosmos DB-svar. <br /> Det finns inga förutbestämda begränsningar från Stream Analytics. |
| Azure Functions   | | Standardbatchstorleken är 262 144 byte (256 KB). <br /> Standardantalet för händelser per batch är 100. <br /> Batchstorleken kan konfigureras och kan ökas eller minskas i [utdataalternativen](#azure-functions)Stream Analytics .

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

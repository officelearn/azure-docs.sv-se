---
title: Förstå utdata från Azure Stream Analytics
description: Den här artikeln beskriver data utdata alternativen som finns i Azure Stream Analytics, inklusive Power BI för analysresultat.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/31/2019
ms.openlocfilehash: 386dc737bb45eec031aaa1a0c55f4478b8302c54
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173578"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Förstå utdata från Azure Stream Analytics

I den här artikeln beskrivs vilka typer av utdata som är tillgängliga för ett Azure Stream Analytics jobb. Utdata kan du lagra och spara resultatet av Stream Analytics-jobb. Med hjälp av utdata kan du utföra ytterligare affärs analyser och data lager hantering av dina data.

När du utformar din Stream Analytics fråga kan du se namnet på utdata genom att använda into- [satsen](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Du kan använda en enda utmatning per jobb eller flera utdata per direkt uppspelnings jobb (om du behöver dem) genom att tillhandahålla flera INTO-satser i frågan.

Om du vill skapa, redigera och testa Stream Analytics jobb-utdata kan du använda [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure POWERSHELL](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)och [Visual Studio](stream-analytics-quick-create-vs.md).

Vissa utmatnings typer [](#partitioning)stöder partitionering. [Batch](#output-batch-size) -storlekarna för utdata varierar för att optimera data flödet.


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen1

Stream Analytics stöder [Azure Data Lake Storage gen 1](../data-lake-store/data-lake-store-overview.md). Azure Data Lake Storage är en storskalig lagrings plats för stor data analys arbets belastningar. Du kan använda Data Lake Storage för att lagra data i valfri storlek, typ och inmatnings hastighet för operativa och undersökande analyser. Stream Analytics måste ha behörighet att komma åt Data Lake Storage.

Azure Data Lake Storage utdata från Stream Analytics är för närvarande inte tillgängligt i regionerna Azure Kina 21Vianet och Azure Tyskland (T-Systems International).

I följande tabell visas egenskaps namn och beskrivningar för att konfigurera Data Lake Storage gen 1-utdata.   

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias | Ett eget namn som används i frågor för att dirigera utdata från frågan till Data Lake Store. |
| Subscription | Den prenumeration som innehåller ditt Azure Data Lake Storage-konto. |
| Kontonamn | Namnet på det Data Lake Stores konto där du vill skicka utdata. En listruta visas med Data Lake Store konton som är tillgängliga i din prenumeration. |
| Prefixmönster för sögväg | Den fil Sök väg som används för att skriva filer inom det angivna Data Lake Store kontot. Du kan ange en eller flera instanser av variablerna {date} och {Time}:<br /><ul><li>Exempel 1: mapp1/logs / {date} / {time}</li><li>Exempel 2: mapp1/logs / {date}</li></ul><br />Tidstämpeln för den skapade mappstrukturen följer UTC och inte lokal tid.<br /><br />Om fil Sök vägens mönster inte innehåller ett avslutande snedstreck (/), behandlas det sista mönstret i fil Sök vägen som ett fil namns prefix. <br /><br />Nya filer skapas i dessa fall:<ul><li>Ändra i utdata-schemat</li><li>Extern eller intern omstart av ett jobb</li></ul> |
| Datumformat | Valfri. Du kan välja datumformat där filerna ordnas om datumtoken används i prefixsökvägen. Exempel: ÅÅÅÅ/MM/DD |
|Tidsformat | Valfri. Om tiden token används i prefixsökvägen, ange tidsformatet där filerna ordnas. Det enda värdet som stöds är för närvarande HH. |
| Händelseserialiseringsformat | Serialiserings formatet för utdata. JSON-, CSV- och Avro stöds.|
| Kodning | Om du använder CSV eller JSON-format måste du ange en kodning. UTF-8 är det enda kodformat som stöds för närvarande.|
| Avgränsare | Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering av CSV-data. Värden som stöds är kommatecken, semikolon, utrymme, fliken och lodrät stapel.|
| Format | Gäller endast för JSON-serialisering. **Raden separerad** anger att utdata är formaterade genom att ha varje JSON-objekt avgränsat med en ny rad. **Matris** anger att utdata är formaterad som en matris med JSON-objekt. Den här matrisen stängs först när jobbet stoppas eller Stream Analytics har gått vidare till nästa tidsfönstret. I allmänhet är det bättre att använda line-separerad JSON, eftersom det inte kräver någon särskild hantering medan utdatafilen fortfarande skrivs till.|
| Autentiseringsläge | Du kan ge åtkomst till ditt Data Lake Storage-konto med hjälp av [hanterad identitet](stream-analytics-managed-identities-adls.md) eller användartoken. När du har beviljat åtkomst kan du återkalla åtkomsten genom att ändra användar kontots lösen ord, ta bort Data Lake Storage utdata för jobbet eller ta bort Stream Analyticss jobbet. |

## <a name="sql-database"></a>SQL Database

Du kan använda [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) som utdata för data som är i relation till typ eller för program som är beroende av innehåll som finns i en Relations databas. Stream Analytics jobb skriver till en befintlig tabell i SQL Database. Tabell schemat måste exakt matcha fälten och deras typer i jobbets utdata. Du kan också ange [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) som utdata via alternativet SQL Database utdata. Information om hur du kan förbättra Skriv data flödet finns i artikeln [Stream Analytics med Azure SQL Database som utdata](stream-analytics-sql-output-perf.md) .

I följande tabell visas egenskaps namnen och deras beskrivning för att skapa en SQL Database utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera utdata till den här databasen. |
| Databas | Namnet på databasen dit du skickar dina utdata. |
| servernamn | SQL Database-servernamn. |
| Användarnamn | Det användar namn som har Skriv behörighet till databasen. Stream Analytics stöder endast SQL-autentisering. |
| lösenordsinställning | Lösenordet för att ansluta till databasen. |
| Tabell | Tabellnamnet där skrivs utdata. Tabell namnet är Skift läges känsligt. Schemat för den här tabellen ska exakt matcha det antal fält och deras typer som jobbets utdata genererar. |
|Ärv partitionsschema| Ett alternativ för att ärva partitionerings schema i föregående fråga-steg för att aktivera helt parallell topologi med flera skrivare till tabellen. Mer information finns i [Azure Stream Analytics utdata till Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Max batchantal| Den rekommenderade övre gränsen för antalet poster som skickas med varje Mass infognings transaktion.|

> [!NOTE]
> Azure SQL Database-erbjudandet stöds för utdata i Stream Analytics, men en virtuell Azure-dator som kör SQL Server med en databas ansluten eller i en SQL Azure hanterad instans stöds inte ännu. Detta kan ändras i framtida versioner.

## <a name="blob-storage-and-azure-data-lake-gen2"></a>Blob Storage och Azure Data Lake Gen2

Utgående till Azure Data Lake Gen2 erbjuds som en förhands gransknings funktion i begränsade regioner över hela världen. Du kan begära åtkomst till förhands granskningen genom att ange ytterligare information i vårt [fråge formulär](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u).

Azure Blob Storage erbjuder en kostnads effektiv och skalbar lösning för att lagra stora mängder ostrukturerade data i molnet. En introduktion till Blob Storage och dess användning finns i [Ladda upp, ladda ned och lista blobar med Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

I följande tabell visas egenskaps namn och beskrivningar för att skapa BLOB-utdata.

| Egenskapsnamn       | Beskrivning                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Utdataalias        | Ett eget namn som används i frågor för att dirigera utdata till blob storage. |
| Lagringskonto     | Namnet på det lagrings konto där du ska skicka dina utdata.               |
| Lagringskontonyckel | Den hemliga nyckeln som är associerade med lagringskontot.                              |
| Lagringscontainer   | En logisk gruppering för blobbar som lagras i Azure-Blob Service. När du laddar upp en blob till Blob-tjänsten måste du ange en behållare för blobben. |
| Sökvägsmönster | Valfritt. Det fil Sök väg mönster som används för att skriva Blobbarna i den angivna behållaren. <br /><br /> I Sök vägs mönstret kan du välja att använda en eller flera instanser av variablerna datum och tid för att ange frekvensen som blobbar skrivs: <br /> {date}, {time} <br /><br />Du kan använda anpassad BLOB-partitionering för att ange ett anpassat {Field}-namn från dina händelse data till partitionering av blobbar. Fältnamnet är alfanumeriska och kan innehålla blanksteg, bindestreck och understreck. Följande: begränsningar för anpassade fält <ul><li>Fält namn är inte Skift läges känsliga. Tjänsten kan till exempel inte skilja mellan kolumn "ID" och kolumn "ID".</li><li>Kapslade fält är inte tillåtna. Använd i stället ett alias i jobb frågan för att "förenkla" fältet.</li><li>Det går inte att använda uttryck som fält namn.</li></ul> <br />Den här funktionen gör det möjligt att använda anpassade inställningar för datum/tid-format i sökvägen. Anpassat datum och tid format måste vara angivna en i taget, omgivna av den {datetime:\<specificerare >} nyckelord. Tillåtna indata för \<specificeraren > är åååå, mm, m, DD, d, hh, H, mm, m, SS eller s. Nyckelordet {datetime\<: specificerare >} kan användas flera gånger i sökvägen för att skapa anpassade konfigurations datum/tids inställningar. <br /><br />Exempel: <ul><li>Exempel 1: cluster1/logs / {date} / {time}</li><li>Exempel 2: cluster1/logs / {date}</li><li>Exempel 3: cluster1/{client_id}/{date}/{time}</li><li>Exempel 4: cluster1/{datetime: SS}/{myField} där frågan är: Välj data. Field som ett fält från indata.</li><li>Exempel 5: cluster1/Year = {datetime: åååå}/månad = {datetime: MM}/dag = {datetime: DD}</ul><br />Tidstämpeln för den skapade mappstrukturen följer UTC och inte lokal tid.<br /><br />Fil namns användning använder följande konvention: <br /><br />{Path Prefix Pattern}/schemaHashcode_Guid_Number.extension<br /><br />Exempel utdatafilerna:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Mer information om den här funktionen finns i [Azure Stream Analytics Anpassad partitionering av BLOB-utdata](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Datumformat | Valfri. Du kan välja datumformat där filerna ordnas om datumtoken används i prefixsökvägen. Exempel: ÅÅÅÅ/MM/DD |
| Tidsformat | Valfri. Om tiden token används i prefixsökvägen, ange tidsformatet där filerna ordnas. Det enda värdet som stöds är för närvarande HH. |
| Händelseserialiseringsformat | Serialiseringsformat för utdata. JSON, CSV, Avro och Parquet stöds. |
|Minsta antal rader (endast Parquet)|Antalet minsta rader per batch. För Parquet skapar varje batch en ny fil. Det aktuella standardvärdet är 2 000 rader och det tillåtna Max värdet är 10 000 rader.|
|Maximal tid (endast Parquet)|Maximal vänte tid per batch. Efter den här tiden kommer batchen att skrivas till utdata även om minimi kravet på rader är uppfyllt. Det aktuella standardvärdet är 1 minut och det högsta tillåtna värdet är 2 timmar. Om BLOB-utdata har Sök vägs mönster frekvens, kan vänte tiden inte vara högre än tidsintervallet för partitionen.|
| Kodning    | Om du använder CSV eller JSON-format måste du ange en kodning. UTF-8 är det enda kodformat som stöds för närvarande. |
| Avgränsare   | Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering av CSV-data. Värden som stöds är kommatecken, semikolon, utrymme, fliken och lodrät stapel. |
| Format      | Gäller endast för JSON-serialisering. **Raden separerad** anger att utdata är formaterade genom att ha varje JSON-objekt avgränsat med en ny rad. **Matris** anger att utdata är formaterad som en matris med JSON-objekt. Den här matrisen stängs först när jobbet stoppas eller Stream Analytics har gått vidare till nästa tidsfönstret. I allmänhet är det bättre att använda line-separerad JSON, eftersom det inte kräver någon särskild hantering medan utdatafilen fortfarande skrivs till. |

När du använder Blob Storage som utdata skapas en ny fil i blobben i följande fall:

* Om filen överskrider det maximala antalet tillåtna block (för närvarande 50 000). Du kan nå maximalt antal tillåtna block utan att nå maximalt tillåten BLOB-storlek. Till exempel om frekvensen av utgående är hög, du kan se mer byte per block och filstorleken är större. Om frekvensen av utgående är låg, varje block har mindre data och filstorleken är mindre.
* Om det finns en schema ändring i utdata och utdataformatet kräver fast schema (CSV och Avro).
* Om ett jobb startas antingen externt av en användare stoppa den och sedan starta den eller internt för återställning av underhåll eller fel.
* Om frågan är helt partitionerad och en ny fil skapas för varje utdata-partition.
* Om användaren tar bort en fil eller en behållare för lagrings kontot.
* Om utdata är tidspartitionerad med hjälp av mönstret för sökvägar och en ny BLOB används när frågan flyttas till nästa timma.
* Om utdata har partitionerats av ett anpassat fält och en ny BLOB skapas per partitionsnyckel om den inte finns.
* Om utdata har partitionerats av ett anpassat fält där-kardinalitet för partitionsnyckel överskrider 8 000 och en ny BLOB skapas per partitionsnyckel.

## <a name="event-hubs"></a>Event Hubs

Den [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) service är en mycket skalbar lösning för publicering-prenumeration händelse. Det kan samla in miljontals händelser per sekund. En användning av en Event Hub som utdata är när utdata från ett Stream Analytics jobb blir indata för ett annat strömmande jobb.

Du behöver några parametrar för att konfigurera data strömmar från Event Hub som utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias | Ett eget namn som används i frågor för att dirigera frågeresultatet till den här händelsehubben. |
| Namnrymd för händelshubb | En behållare för en uppsättning meddelande enheter. När du skapade en ny händelsehubben, skapade du även ett namn område för Event Hub. |
| Namn på händelsehubb | Namnet på din Event Hub-utdata. |
| Principnamn för Event hub | Principen för delad åtkomst, som du kan skapa på fliken **Konfigurera** på händelsehubben. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. |
| Principnyckel för Event hub | Den delade åtkomst nyckeln som används för att autentisera åtkomsten till Event Hub-namnområdet. |
| Partitionsnyckelkolumn | Valfritt. En kolumn som innehåller partitionsnyckel för Event Hub-utdata. |
| Händelseserialiseringsformat | Serialiserings formatet för utdata. JSON-, CSV- och Avro stöds. |
| Kodning | För CSV och JSON är UTF-8 i kodningsformat som endast stöds just nu. |
| Avgränsare | Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering av data i CSV-format. Värden som stöds är kommatecken, semikolon, utrymme, fliken och lodrät stapel. |
| Format | Gäller endast för JSON-serialisering. **Raden separerad** anger att utdata är formaterade genom att ha varje JSON-objekt avgränsat med en ny rad. **Matris** anger att utdata är formaterad som en matris med JSON-objekt. Den här matrisen stängs först när jobbet stoppas eller Stream Analytics har gått vidare till nästa tidsfönstret. I allmänhet är det bättre att använda line-separerad JSON, eftersom det inte kräver någon särskild hantering medan utdatafilen fortfarande skrivs till. |
| Egenskapskolumner | Valfritt. Kommaavgränsade kolumner som måste bifogas som användar egenskaper för det utgående meddelandet i stället för nytto lasten. Mer information om den här funktionen finns i avsnittet [anpassade metadata för utdata](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI

Du kan använda [Power BI](https://powerbi.microsoft.com/) som utdata för ett Stream Analytics jobb för att tillhandahålla en omfattande visualiserings upplevelse av analys resultaten. Du kan använda den här funktionen för instrument paneler för drift, rapportgenerering och måttbaserade rapporter.

Power BI utdata från Stream Analytics är för närvarande inte tillgängligt i regionerna Azure Kina 21Vianet och Azure Tyskland (T-Systems International).

I följande tabell visas egenskaps namn och beskrivningar för att konfigurera Power BI-utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ange ett eget namn som används i frågor för att dirigera frågeresultatet till Power BI utdata. |
| Grupparbetsyta |Om du vill aktivera delning av data med andra Power BI användare kan du välja grupper inuti ditt Power BI konto eller välja **min arbets yta** om du inte vill skriva till en grupp. Uppdaterar en befintlig grupp kräver förnya Power BI-autentisering. |
| Namn på datauppsättning |Ange ett namn på data uppsättningen som du vill att Power BI-utdata ska använda. |
| Tabellnamn |Ange ett tabellnamn under datauppsättningen för Power BI-utdata. För närvarande kan Power BI utdata från Stream Analytics-jobb bara ha en tabell i en data uppsättning. |
| Auktorisera anslutningen | Du måste auktorisera med Power BI för att konfigurera dina inställningar för utdata. När du har beviljat dessa utdata åtkomst till din Power BI-instrumentpanel kan du återkalla åtkomsten genom att ändra användar kontots lösen ord, ta bort jobbets utdata eller ta bort Stream Analyticss jobbet. | 

En genom gång av hur du konfigurerar en Power BI utdata och en instrument panel finns i själv studie kursen [Azure Stream Analytics och Power BI](stream-analytics-power-bi-dashboard.md) .

> [!NOTE]
> Skapa inte uttryckligen data uppsättningen och tabellen i Power BI instrument panelen. Data uppsättningen och tabellen fylls i automatiskt när jobbet startas och jobbet börjar pumpa utdata till Power BI. Om jobb frågan inte genererar några resultat skapas inte data uppsättningen och tabellen. Om Power BI redan har en data uppsättning och en tabell med samma namn som det som angavs i det här Stream Analyticss jobbet, skrivs befintliga data över.
>

### <a name="create-a-schema"></a>Skapa ett schema
Azure Stream Analytics skapar en Power BI data uppsättning och ett tabell schema för användaren om de inte redan finns. I annat fall uppdateras tabellen med nya värden. För närvarande kan endast en tabell finnas i en data uppsättning. 

Power BI använder bevarande principen First-in, First-Out (FIFO). Data samlas in i en tabell tills den når 200 000 rader.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Konvertera en datatyp från Stream Analytics till Power BI
Azure Stream Analytics uppdaterar datamodellen dynamiskt vid körning om utdata-schemat ändras. Alla spåras kolumnen namnändringar, kolumnen ändras, och tillägg och borttagning av kolumner.

Den här tabellen innehåller data typ konverteringar från [Stream Analytics data typer](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics) till Power BI [typer av enhets data modeller (EDM)](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)om det inte finns någon Power BI data uppsättning och tabell.

Från Stream Analytics | Power BI
-----|-----
bigint | Int64
nvarchar(max) | Sträng
datetime | DateTime
flyt | Double-värde
Post-matris | Sträng typ, konstant värde "IRecord" eller "IArray"

### <a name="update-the-schema"></a>Uppdatera schemat
Stream Analytics härleder modellschemat data baserat på den första uppsättningen händelser i utdata. Senare, om det behövs, uppdateras data modellens schema för att hantera inkommande händelser som kanske inte passar in i det ursprungliga schemat.

`SELECT *` Undvik frågan för att förhindra dynamisk schema uppdatering på rader. Förutom potentiella prestanda konsekvenser kan det leda till osäkerhet vid den tid det tar för resultaten. Välj de exakta fält som ska visas på instrument panelen för Power BI. Datavärdena bör dessutom vara kompatibel med datatypen.


Föregående/aktuell | Int64 | Sträng | DateTime | Double-värde
-----------------|-------|--------|----------|-------
Int64 | Int64 | Sträng | Sträng | Double-värde
Double-värde | Double-värde | Sträng | Sträng | Double-värde
Sträng | String | String | String | Sträng 
DateTime | Sträng | Sträng |  DateTime | Sträng

## <a name="table-storage"></a>Tabellagring

[Azure Table Storage](../storage/common/storage-introduction.md) erbjuder hög skalbar lagring med hög tillgänglighet, så att ett program kan skalas automatiskt för att möta användarnas behov. Table Storage är Microsofts NoSQL Key/Attribute-Arkiv, som du kan använda för strukturerade data med färre begränsningar i schemat. Azure Table storage kan användas för att lagra data för persistence och effektiv Filhämtning.

I följande tabell visas egenskaps namnen och deras beskrivningar för att skapa ett tabell resultat.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera utdata till table storage. |
| Lagringskonto |Namnet på det lagrings konto där du ska skicka dina utdata. |
| Lagringskontonyckel |Åtkomstnyckeln som är associerade med lagringskontot. |
| Tabellnamn |Namnet på tabellen. Tabellen skapas om den inte finns. |
| Partitionsnyckeln |Namnet på kolumnen utdata som innehåller partitionsnyckel. Partitionsnyckel är en unik identifierare för partitionen i en tabell som utgör den första delen av en entitets primära nyckel. Det är ett sträng värde som kan vara upp till 1 KB stort. |
| Radnyckel |Namnet på kolumnen utdata som innehåller rad nyckeln. Rad nyckeln är en unik identifierare för en entitet i en partition. Den utgör den andra delen av en entitets primärnyckel. Rad nyckeln är ett sträng värde som kan vara upp till 1 KB stort. |
| Batchstorlek |Antalet poster för en batchåtgärd. Standardvärdet (100) är tillräckliga för de flesta jobb. Mer information om hur du ändrar den här inställningen finns i [specifikationen tabell åtgärds åtgärd](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._table_batch_operation) . |

## <a name="service-bus-queues"></a>Service Bus-köer

[Service Bus köer](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) erbjuder en leverans av FIFO-meddelanden till en eller flera konkurrerande konsumenter. Normalt tas meddelanden emot och bearbetas av mottagarna i den temporala ordning som de lades till i kön. Varje meddelande tas emot och bearbetas bara av en meddelande konsument.

I följande tabell visas egenskaps namnen och deras beskrivningar för att skapa utdata från kön.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera frågeresultatet till den här Service Bus kön. |
| Service Bus-namnområde |En behållare för en uppsättning meddelande enheter. |
| Könamn |Namnet på Service Bus kön. |
| Köprincipnamn |När du skapar en kö kan du också skapa principer för delad åtkomst på fliken **Konfigurera** i kön. Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. |
| Köprincipnyckel |Den delade åtkomstnyckeln som används för att autentisera åtkomsten till Service Bus-namnområdet. |
| Händelseserialiseringsformat |Serialiserings formatet för utdata. JSON-, CSV- och Avro stöds. |
| Kodning |För CSV och JSON är UTF-8 i kodningsformat som endast stöds just nu. |
| Avgränsare |Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering av data i CSV-format. Värden som stöds är kommatecken, semikolon, utrymme, fliken och lodrät stapel. |
| Format |Gäller endast för JSON-typ. **Raden separerad** anger att utdata är formaterade genom att ha varje JSON-objekt avgränsat med en ny rad. **Matris** anger att utdata är formaterad som en matris med JSON-objekt. |
| Egenskapskolumner | Valfritt. Kommaavgränsade kolumner som måste bifogas som användar egenskaper för det utgående meddelandet i stället för nytto lasten. Mer information om den här funktionen finns i avsnittet [anpassade metadata för utdata](#custom-metadata-properties-for-output). |
| Systemegenskapskolumner | Valfritt. Nyckel värdes par med system egenskaper och motsvarande kolumn namn som måste kopplas till det utgående meddelandet i stället för nytto lasten. Mer information om den här funktionen finns i avsnittet [system egenskaper för Service Bus kö-och ämnes utdata](#system-properties-for-service-bus-queue-and-topic-outputs)  |

Antalet partitioner är [baserat på Service Bus-SKU och storleken](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckeln är ett heltalsvärde som unikt för varje partition.

## <a name="service-bus-topics"></a>Avsnitt om Service Bus
Service Bus köer tillhandahåller en en-till-en-kommunikations metod från sändare till mottagare. [Service Bus ämnen](https://msdn.microsoft.com/library/azure/hh367516.aspx) innehåller en en-till-många-form av kommunikation.

I följande tabell visas egenskaps namnen och deras beskrivningar för att skapa ett Service Bus avsnitts utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera frågeresultatet till den här Service Bus ämnet. |
| Service Bus-namnområde |En behållare för en uppsättning meddelande enheter. När du har skapat en ny händelsehubb, skapade du även en Service Bus-namnrymd. |
| Ämnesnamn |Ämnen är meddelandeentiteter, liknande event hubs och köer. De är utformade för att samla in händelse strömmar från enheter och tjänster. När ett ämne skapas får det också ett särskilt namn. Meddelanden som skickas till ett ämne är inte tillgängliga om inte en prenumeration skapas, så se till att det finns en eller flera prenumerationer i avsnittet. |
| Ämnesprincipnamn |När du skapar ett Service Bus ämne kan du också skapa principer för delad åtkomst på fliken **Konfigurera** . Varje princip för delad åtkomst har ett namn, behörigheter som du ställa in och åtkomstnycklar. |
| Ämnesprincipnyckel |Den delade åtkomstnyckeln som används för att autentisera åtkomsten till Service Bus-namnområdet. |
| Händelseserialiseringsformat |Serialiserings formatet för utdata. JSON-, CSV- och Avro stöds. |
| Kodning |Om du använder CSV eller JSON-format måste du ange en kodning. UTF-8 är det enda kodformat som stöds för närvarande. |
| Avgränsare |Gäller endast för CSV-serialisering. Stream Analytics stöder ett antal olika avgränsare för serialisering av data i CSV-format. Värden som stöds är kommatecken, semikolon, utrymme, fliken och lodrät stapel. |
| Egenskapskolumner | Valfritt. Kommaavgränsade kolumner som måste bifogas som användar egenskaper för det utgående meddelandet i stället för nytto lasten. Mer information om den här funktionen finns i avsnittet [anpassade metadata för utdata](#custom-metadata-properties-for-output). |
| Systemegenskapskolumner | Valfritt. Nyckel värdes par med system egenskaper och motsvarande kolumn namn som måste kopplas till det utgående meddelandet i stället för nytto lasten. Mer information om den här funktionen finns i avsnittet [system egenskaper för Service Bus kö-och ämnes utdata](#system-properties-for-service-bus-queue-and-topic-outputs) |

Antalet partitioner är [baserat på Service Bus-SKU och storleken](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckel är ett unikt heltals värde för varje partition.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) är en globalt distribuerad databas tjänst som erbjuder obegränsad elastisk skalning runt om i världen, frågan och automatisk indexering över schema oberoende data modeller. Mer information om Azure Cosmos DB container alternativ för Stream Analytics finns i artikeln [Stream Analytics med Azure Cosmos DB som utdata](stream-analytics-documentdb-output.md) .

Azure Cosmos DB utdata från Stream Analytics är för närvarande inte tillgängligt i regionerna Azure Kina 21Vianet och Azure Tyskland (T-Systems International).

> [!Note]
> För tillfället stöder Azure Stream Analytics endast anslutning till Azure Cosmos DB med hjälp av SQL-API: et.
> Andra Azure Cosmos DB API: er stöds inte ännu. Om du punkt Azure Stream Analytics till Azure Cosmos DB-konton som skapats med andra API: er, kanske data inte korrekt lagras.

I följande tabell beskrivs egenskaperna för att skapa ett Azure Cosmos DB-utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias | Ett alias till se detta utdata i din Stream Analytics-fråga. |
| Kanalmottagare | Azure Cosmos DB. |
| Importalternativ | Välj antingen **välj Cosmos dB från din prenumeration** eller **Ange Cosmos DB inställningar manuellt**.
| Konto-ID | Azure Cosmos DB kontots namn eller slut punkts-URI. |
| Kontonyckel | Den delade åtkomst nyckeln för det Azure Cosmos DB kontot. |
| Databas | Namnet på Azure Cosmos DBs databasen. |
| Behållarnamn | Behållar namnet som ska användas, vilket måste finnas i Cosmos DB. Exempel:  <br /><ul><li> _MyContainer_: Det måste finnas en behållare med namnet "behållare".</li>|
| Dokument-id |Valfritt. Namnet på fältet i utmatnings händelser som används för att ange den primära nyckel som infognings-eller uppdaterings åtgärder baseras på.

## <a name="azure-functions"></a>Azure Functions
Azure Functions är en server lös beräknings tjänst som du kan använda för att köra kod på begäran utan att behöva etablera eller hantera infrastruktur. Du kan implementera kod som utlöses av händelser som inträffar i Azure-eller partner tjänster. Den här möjligheten att Azure Functions att svara på utlösare gör det till ett naturligt resultat för Azure Stream Analytics. Med det här nätverkskortet kan användarna ansluta Stream Analytics till Azure Functions och köra ett skript eller ett kod avsnitt som svar på en rad olika händelser.

Azure Functions utdata från Stream Analytics är för närvarande inte tillgängligt i regionerna Azure Kina 21Vianet och Azure Tyskland (T-Systems International).

Azure Stream Analytics anropar Azure Functions via HTTP-utlösare. Azure Functions-utmatnings kortet är tillgängligt med följande konfigurerbara egenskaper:

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Funktionsapp |Namnet på din Azure Functions-app. |
| Funktion |Namnet på funktionen i din Azure Functions-app. |
| Nyckel |Om du vill använda en Azure-funktion från en annan prenumeration kan du göra det genom att ange nyckeln för att få åtkomst till din funktion. |
| Max batchstorlek |En egenskap som låter dig ange den maximala storleken för varje utgående batch som skickas till din Azure-funktion. Den inkommande enheten är i byte. Som standard är det här värdet 262 144 byte (256 KB). |
| Max batchantal  |En egenskap som gör att du kan ange det maximala antalet händelser i varje batch som skickas till Azure Functions. Standardvärdet är 100. |

När Azure Stream Analytics tar emot en 413-undantag ("http-begäran är för stor") från en Azure-funktion minskar storleken på batcharna som den skickar till Azure Functions. Använd det här undantaget för att se till att Azure Stream Analytics inte skickar för stora batchar i Azure function-koden. Kontrol lera också att maximalt antal batchar och storleks värden som används i funktionen stämmer överens med de värden som anges i Stream Analytics portalen.

> [!NOTE]
> Under test anslutningen skickar Stream Analytics en tom batch som Azure Functions för att testa om anslutningen mellan de två fungerar. Se till att din functions-app hanterar tomma batch-begäranden för att kontrol lera att test anslutningen lyckas.

I en situation där det inte finns någon händelse vilplan i ett tids fönster genereras inga utdata. Därför anropas inte funktionen **computeResult** . Det här beteendet är konsekvent med de inbyggda mängdfunktionerna.

## <a name="custom-metadata-properties-for-output"></a>Anpassade egenskaper för metadata för utdata 

Du kan koppla frågeegenskaper som användar egenskaper till dina utgående meddelanden. De här kolumnerna hamnar inte i nytto lasten. Egenskaperna finns i form av en ord lista i utmatnings meddelandet. *Key* är kolumnens namn och *värde* är kolumnens värde i ord listan för egenskaper. Alla Stream Analytics data typer stöds förutom post och matris.  

Utdata som stöds: 
* Service Bus-kö 
* Service Bus-ämne 
* Händelsehub 

I följande exempel lägger vi till de två fälten `DeviceId` och `DeviceStatus` i metadata. 
* Frågeterm`select *, DeviceId, DeviceStatus from iotHubInput`
* Konfiguration av utdata:`DeviceId,DeviceStatus`

![Egenskapskolumner](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

Följande skärm bild visar egenskaper för utmatnings meddelande som har granskats i EventHub genom [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

![Anpassade egenskaper för händelse](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="system-properties-for-service-bus-queue-and-topic-outputs"></a>System egenskaper för Service Bus kö-och ämnes utdata 
Du kan koppla frågenoder som [system egenskaper](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) till den utgående Service Bus-kön eller ämnes meddelanden. De här kolumnerna hamnar inte i nytto lasten i stället för motsvarande BrokeredMessage [system egenskap](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties) fylls med värdena i kolumnen fråga.
Dessa system egenskaper stöds – `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`.
Sträng värden för de här kolumnerna tolkas som motsvarande typ av system egenskaps värde och eventuella tolknings fel behandlas som data fel.
Det här fältet anges som ett JSON-objekt format. Information om det här formatet är följande:
* Omgivet av klammerparenteser {}.
* Skrivet i nyckel/värde-par.
* Nycklar och värden måste vara strängar.
* Nyckel är systemets egenskaps namn och värde är frågans kolumn namn.
* Nycklar och värden avgränsas med kolon.
* Varje nyckel/värde-par avgränsas med ett kommatecken.

Här visas hur du använder den här egenskapen –

* Frågeterm`select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Kolumner i system egenskap:`{ "MessageId": "column1", "PartitionKey": "column2"}`

Detta anger `MessageId` om Service Bus Queue-meddelanden med `column1`värdena och PartitionKey har angetts med `column2`värdet.

## <a name="partitioning"></a>Partitionering

I följande tabell sammanfattas partition-stöd och antalet skrivare för utdata för varje Utdatatyp av:

| Utdatatyp | Partitionering | Partitionsnyckeln  | Antalet utdata-skrivare |
| --- | --- | --- | --- |
| Azure Data Lake Store | Ja | Använd {date}-och {Time}-tokens i mönstret Path-prefix. Välj datum format, till exempel ÅÅÅÅ/MM/DD, DD/MM/ÅÅÅÅ eller MM-DD-ÅÅÅÅ. HH används för tids formatet. | Följer inkommande partitionering för [helt kan frågor](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Ja, måste aktive ras. | Baserat på PARTITION BY-satsen i frågan. | När alternativet Ärv partitionering är aktiverat, följer du inpartitionering för [fullständiga kan göras parallella-frågor](stream-analytics-scale-jobs.md). Mer information om hur du uppnår bättre Skriv data flödes prestanda när du läser in data i Azure SQL Database finns [Azure Stream Analytics utdata till Azure SQL Database](stream-analytics-sql-output-perf.md). |
| Azure Blob Storage | Ja | Använd {date}-och {Time}-token från dina händelse fält i Sök vägs mönstret. Välj datum format, till exempel ÅÅÅÅ/MM/DD, DD/MM/ÅÅÅÅ eller MM-DD-ÅÅÅÅ. HH används för tids formatet. BLOB-utdata kan partitioneras med ett enda anpassat Event-attribut {FieldName} eller {datetime\<: specificerare >}. | Följer inkommande partitionering för [helt kan frågor](stream-analytics-scale-jobs.md). |
| Azure Event Hubs | Ja | Ja | Varierar beroende på partitionen justering.<br /> När partitionsnyckel för Event Hub-utdata är jämnt justerad med föregående steg i frågan, är antalet skrivare samma som antalet partitioner i Event Hub-utdata. Varje skrivare använder [klassen EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) för att skicka händelser till den angivna partitionen. <br /> När partitionsnyckel för utdata i Event Hub inte är justerad till föregående steg i frågan, är antalet skrivare samma som antalet partitioner i det föregående steget. Varje skrivare använder [klassen SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) i **EventHubClient** för att skicka händelser till alla utdata-partitioner. |
| Power BI | Nej | Ingen | Ej tillämpligt. |
| Azure Table Storage | Ja | Alla utdatakolumn.  | Följer inkommande partitionering för [fullständigt parallelliseras frågor](stream-analytics-scale-jobs.md). |
| Azure Service Bus-ämne | Ja | Valt automatiskt. Antalet partitioner är baserad på den [Service Bus-SKU och storlek](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckel är ett unikt heltals värde för varje partition.| Samma som antalet partitioner i avsnittet om utdata.  |
| Azure Service Bus-kö | Ja | Valt automatiskt. Antalet partitioner är baserad på den [Service Bus-SKU och storlek](../service-bus-messaging/service-bus-partitioning.md). Partitionsnyckel är ett unikt heltals värde för varje partition.| Samma som antalet partitioner i den utgående kön. |
| Azure Cosmos DB | Ja | Baserat på PARTITION BY-satsen i frågan. | Följer inkommande partitionering för [fullständigt parallelliseras frågor](stream-analytics-scale-jobs.md). |
| Azure Functions | Ja | Baserat på PARTITION BY-satsen i frågan. | Följer inkommande partitionering för [fullständigt parallelliseras frågor](stream-analytics-scale-jobs.md). |

Antalet utgående skrivare kan också kontrol leras med hjälp `INTO <partition count>` av (se [i](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count))-satsen i din fråga, vilket kan vara till hjälp när du vill uppnå en önskad jobb sto pol Ogin. Om nätverkskortet utdata inte är partitionerad medför brist på data i en indatapartitionen en fördröjning upp till sent ankomst lång tid. I sådana fall slås utdata samman till en enda skrivare, vilket kan orsaka Flask halsar i din pipeline. Om du vill veta mer om principen för att komma i beaktande, se [Azure Stream Analytics händelse ordning](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Batchstorlek för utdata
Azure Stream Analytics använder batchar av variabel storlek för att bearbeta händelser och skriva till utdata. Vanligt vis skriver Stream Analyticss motorn ett meddelande i taget och använder batchar för effektivitet. När hastigheten för både inkommande och utgående händelser är hög använder Stream Analytics större batchar. När den utgående hastigheten är låg, använder mindre Batcher för att hålla det med låg latens.

I följande tabell förklaras några överväganden för batching av utdata:

| Utdatatyp | Maxstorlek på meddelande | Optimering av batch-storlek |
| :--- | :--- | :--- |
| Azure Data Lake Store | Se [data Lake Storage gränser](../azure-subscription-service-limits.md#data-lake-store-limits). | Använd upp till 4 MB per Skriv åtgärd. |
| Azure SQL Database | Kan konfigureras med maximalt antal batchar. 10 000 högsta och 100 lägsta antal rader per enskild Mass infogning som standard.<br />Se [Azure SQL-gränser](../sql-database/sql-database-resource-limits.md). |  Varje batch infogas från början med maximalt antal batchar. Batch är uppdelad i mitten (till lägsta antal batchar) baserat på nya försök att köra fel från SQL. |
| Azure Blob Storage | Se [Azure Storage gränser](../azure-subscription-service-limits.md#storage-limits). | Maximal blob-block storlek är 4 MB.<br />Maximalt antal BLOB-bock är 50 000. |
| Azure Event Hubs  | 256 KB eller 1 MB per meddelande. <br />Se [Event Hubs gränser](../event-hubs/event-hubs-quotas.md). |  När indata/utdata-partitionering inte är justerad, paketeras varje `EventData` händelse individuellt i och skickas i en batch med upp till maximal meddelande storlek. Detta inträffar även om [anpassade metadata-egenskaper](#custom-metadata-properties-for-output) används. <br /><br />  När indata/utdata-partitionering är justerad, packas flera händelser i `EventData` en enda instans, upp till den maximala meddelande storleken och skickas. |
| Power BI | Se [Power BI REST API-gränser](https://msdn.microsoft.com/library/dn950053.aspx). |
| Azure Table Storage | Se [Azure Storage gränser](../azure-subscription-service-limits.md#storage-limits). | Standardvärdet är 100 entiteter per enskild transaktion. Du kan konfigurera den till ett mindre värde efter behov. |
| Azure Service Bus-kö   | 256 KB per meddelande för standard nivån 1 MB för Premium-nivån.<br /> Se [Service Bus gränser](../service-bus-messaging/service-bus-quotas.md). | Använd en enskild händelse per meddelande. |
| Azure Service Bus-ämne | 256 KB per meddelande för standard nivån 1 MB för Premium-nivån.<br /> Se [Service Bus gränser](../service-bus-messaging/service-bus-quotas.md). | Använd en enskild händelse per meddelande. |
| Azure Cosmos DB   | Se [Azure Cosmos dB gränser](../azure-subscription-service-limits.md#azure-cosmos-db-limits). | Batchstorlek och skriv frekvens justeras dynamiskt baserat på Azure Cosmos DB svar. <br /> Det finns inga identifierade begränsningar från Stream Analytics. |
| Azure Functions   | | Standard grupp storleken är 262 144 byte (256 KB). <br /> Standard antalet händelser per batch är 100. <br /> Batchstorlek är konfigurerbar och kan ökas eller minskas i Stream Analytics [Utdataalternativ](#azure-functions).

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> 
> [Snabbstart: Skapa ett Stream Analytics jobb genom att använda Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

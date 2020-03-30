---
title: Import och datainmatning i sökindex
titleSuffix: Azure Cognitive Search
description: Fylla i och ladda upp data till ett index i Azure Cognitive Search från externa datakällor.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3f38e9bb96ce76263a3124f8bfdc49dc638bfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282762"
---
# <a name="data-import-overview---azure-cognitive-search"></a>Översikt över dataimport - Azure Cognitive Search

I Azure Cognitive Search körs frågor över ditt innehåll som läses in och sparas i ett [sökindex](search-what-is-an-index.md). I den här artikeln undersöks de två grundläggande metoderna för att fylla i ett index: *skicka* dina data till indexet programmässigt eller peka en [Azure Cognitive Search-indexerare](search-indexer-overview.md) på en datakälla som stöds för att *hämta* data.

Med någon av tillvägagångssätten är målet att *läsa in data* från en extern datakälla i ett Azure Cognitive Search-index. Azure Cognitive Search låter dig skapa ett tomt index, men tills du skickar eller hämtar data till det är det inte frågebart.

## <a name="pushing-data-to-an-index"></a>Skicka data till ett index
Push-modellen, som används för att programmässigt skicka dina data till Azure Cognitive Search, är den mest flexibla metoden. För det första finns det inga begränsningar på vilken typ av datakälla som får användas. Alla datauppsättningar som består av JSON-dokument kan skickas till ett Azure Cognitive Search-index, förutsatt att varje dokument i datauppsättningen har fältmappning till fält som definierats i indexschemat. För det andra finns det inga begränsningar på körningsfrekvensen. Du kan skicka ändringar till ett index så ofta du vill. För program som har mycket låga fördröjningskrav (t.ex. om det är viktigt att sökåtgärder är synkroniserade med dynamiska inventeringsdatabaser) är push-modellen ditt enda alternativ.

Den här metoden är mer flexibel än pull-modellen eftersom du kan ladda upp dokument individuellt eller i batchar (upp till 1 000 per batch eller 16 MB, beroende på vilken gräns som nås först). Med push-modellen kan du också ladda upp dokument till Azure Cognitive Search oavsett var dina data finns.

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>Så här skickar du data till ett Azure Cognitive Search-index

Du kan använda följande API:er för att läsa in ett eller flera dokument i ett index:

+ [Lägg till, uppdatera eller ta bort dokument (REST API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [indexAction-klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) eller [indexBatch-klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Det finns för närvarande inget verktygsstöd för att skicka data via portalen.

En introduktion till varje metod finns i [Snabbstart: Skapa ett Azure Cognitive Search-index med PowerShell](search-create-index-rest-api.md) eller [C# Quickstart: Skapa ett Azure Cognitive Search-index med .NET SDK](search-get-started-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Indexeringsåtgärder: ladda upp, slå samman, mergeOrUpload, ta bort

Du kan styra typen av indexeringsåtgärd per dokument och ange om dokumentet ska överföras i sin helhet, sammanfogas med befintligt dokumentinnehåll eller tas bort.

I REST API: et utfärda HTTP POST-begäranden med JSON-begärandeorgan till slutpunkts-URL:en för Azure Cognitive Search-index. Varje JSON-objekt i matrisen "värde" innehåller dokumentets nyckel och anger om en indexeringsåtgärd lägger till, uppdaterar eller tar bort dokumentinnehåll. Ett kodexempel finns [i Läsa in dokument](search-get-started-dotnet.md#load-documents).

I .NET SDK paketerar du `IndexBatch` dina data till ett objekt. En `IndexBatch` kapslar in en `IndexAction` samling objekt, som var och en innehåller ett dokument och en egenskap som talar om för Azure Cognitive Search vilken åtgärd som ska utföras i dokumentet. Ett kodexempel finns i [snabbstarten C#](search-get-started-dotnet.md).


| @search.action | Beskrivning | Nödvändiga fält för varje dokument | Anteckningar |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |En `upload`-åtgärd liknar en ”upsert” där dokumentet infogas om det är nytt och uppdateras/ersätts om det finns. |nyckel plus eventuella andra fält som du vill definiera |När du uppdaterar och ersätter ett befintligt dokument tilldelas alla fält som inte angetts i begäran `null`. Detta sker även om fältet tidigare hade ett värde som inte var null. |
| `merge` |Uppdaterar ett befintligt dokument med de angivna fälten. Sammanfogningen misslyckas om dokumentet inte finns i indexet. |nyckel plus eventuella andra fält som du vill definiera |Alla fält som du anger i en sammanfogning ersätter det befintliga fältet i dokumentet. I .NET SDK innehåller detta `DataType.Collection(DataType.String)`fält av typen . I REST API innehåller detta `Collection(Edm.String)`fält av typen . Om dokumentet till exempel innehåller ett `tags`-fält med värdet `["budget"]` och du utför en sammanfogning med värdet `["economy", "pool"]` för `tags` så blir det slutliga värdet för fältet `tags``["economy", "pool"]`. Det blir inte `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Den här åtgärden fungerar som `merge` om ett dokument med den angivna nyckeln redan finns i indexet. Om dokumentet inte finns fungerar den som `upload` med ett nytt dokument. |nyckel plus eventuella andra fält som du vill definiera |- |
| `delete` |Tar bort det angivna dokumentet från indexet. |endast nyckel |Andra fält som du anger än nyckelfältet ignoreras. Om du vill ta bort ett enstaka fält från ett dokument använder du `merge` i stället och anger bara fältet till null. |

## <a name="decide-which-indexing-action-to-use"></a>Bestäm vilken indexeringsåtgärd som du vill använda
Så här importerar du data med .NET SDK (ladda upp, sammanfoga, ta bort och slå sammanOrUpload). Beroende på vilken av åtgärderna nedan som du väljer måste endast vissa fält tas med för varje dokument:


### <a name="formulate-your-query"></a>Formulera frågan
Du kan [söka i ditt index med hjälp av REST-API:et](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) på två sätt. Ett sätt är att skicka en HTTP POST-begäran där dina frågeparametrar definieras i ett JSON-objekt i begärandetexten. Det andra sättet är att skicka en HTTP GET-begäran där dina frågeparametrar definieras i URL:en för begäran. POST har mindre [restriktiva gränser](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) vad gäller frågeparametrarnas storlek än GET. Av den anledningen rekommenderar vi att du använder POST såvida det inte finns särskilda omständigheter som gör att GET är lämpligare.

För både POST och GET måste du ange *tjänstnamnet*, *indexnamnet* och *API-versionen* (den aktuella API-versionen är `2019-05-06` vid tidpunkten för publiceringen av det här dokumentet) i URL:en för begäran. För GET anger du frågeparametrarna i *frågesträngen* i slutet av URL:en. Se URL-formatet nedan:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06

Formatet för POST är samma, men med endast ”api-version” i frågesträngsparametrarna.


## <a name="pulling-data-into-an-index"></a>Hämta in data till ett index
Datahämtningsmodellen crawlar en datakälla som stöds och överför automatiskt data till ditt index. I Azure Cognitive Search implementeras den här funktionen via *indexerare*, som för närvarande är tillgänglig för dessa plattformar:

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Azure SQL Database och SQL Server på virtuella Azure-datorer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Indexerare ansluter ett index till en datakälla (vanligtvis en tabell, vy eller motsvarande struktur) och mappar källfält till motsvarande fält i indexet. Under körningen omvandlas raduppsättningen automatiskt till JSON och läses in i det angivna indexet. Alla indexerare stöder schemaläggning så att du kan ange hur ofta data ska uppdateras. De flesta indexerare tillhandahåller ändringsspårning om datakällan har stöd för det. Indexerare spårar ändringar och borttagningar av befintliga dokument och identifierar nya dokument, vilket gör att du slipper hantera dina data i indexet aktivt. 


### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>Så här hämtar du data till ett Azure Cognitive Search-index

Indexerarfunktioner exponeras på [Azure Portal](search-import-data-portal.md), i [REST-API:et](/rest/api/searchservice/Indexer-operations) och i [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions). 

En fördel med att använda portalen är att Azure Cognitive Search vanligtvis kan generera ett standardindexschema för dig genom att läsa metadata för källdatauppsättningen. Du kan ändra det genererade indexet tills indexet har bearbetats. Därefter är de enda schemaändringarna som tillåts de som inte kräver omindexering. Om de ändringar som du vill göra påverkar schemat direkt måste indexet återskapas. 

## <a name="verify-data-import-with-search-explorer"></a>Verifiera dataimport med Sökutforskaren

Ett snabbt sätt att göra en preliminär kontroll av dokumentöverföringen är att använda **Sökutforskaren** i portalen. Med utforskaren kan du köra frågor mot ett index utan att behöva skriva kod. Sökupplevelsen baseras på standardinställningar, till exempel den [enkla parametern för](/rest/api/searchservice/simple-query-syntax-in-azure-search) [sökmode-sökmode](/rest/api/searchservice/search-documents). Resultaten returneras i JSON så att du kan gå igenom hela dokumentet.

> [!TIP]
> Många [Azure Cognitive Search-kodexempel](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) innehåller inbäddade eller lättillgängliga datauppsättningar, vilket ger ett enkelt sätt att komma igång. Portalen innehåller också en exempelindexerare och datakälla som består av en liten datauppsättning med fastighetsinformation (med namnet ”realestate-us-sample”). När du kör den förkonfigurerade indexeraren på exempeldatakällan skapas ett index och läses in med dokument som sedan kan efterfrågas i Sökutforskaren eller efter kod som du skriver.

## <a name="see-also"></a>Se även

+ [Översikt över indexeraren](search-indexer-overview.md)
+ [Steg-för-steg-beskrivning av portalen: skapa, läsa in och avfråga ett index](search-get-started-portal.md)

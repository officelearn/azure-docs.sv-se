---
title: Importera data för datainläsning till ett search-index – Azure Search
description: Fyll i och ladda upp data till ett index i Azure Search från externa datakällor.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7d95ae1f750c59c121e998c6f51f9439b1b0339a
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287102"
---
# <a name="data-import-overview---azure-search"></a>Importera data översikt – Azure Search

I Azure Search körs frågorna innehåll som lästs in i och sparas i en [sökindex](search-what-is-an-index.md). Den här artikeln går igenom två grundläggande sätt för att fylla ett index: *push* dina data till indexet programmässigt, eller peka en [Azure Search-indexerare](search-indexer-overview.md) på en datakälla som stöds till  *pull* i data.

Med antingen metoden målet är att *webbsidesinläsning* från en extern datakälla i ett Azure Search-index. Azure Search kan du skapa ett tomt index, men tills du skicka eller hämta data till den den är inte frågningsbar.

## <a name="pushing-data-to-an-index"></a>Skicka data till ett index
Push-modellen, som används för att programmatiskt skicka data till Azure Search, är den mest flexibla metoden. För det första finns det inga begränsningar på vilken typ av datakälla som får användas. En datauppsättning som består av JSON-dokument kan skickas till ett Azure Search-index, förutsatt att alla dokument i datauppsättningen har fält som mappar till fälten som anges i indexschemat. För det andra finns det inga begränsningar på körningsfrekvensen. Du kan skicka ändringar till ett index så ofta du vill. För program som har mycket låga fördröjningskrav (t.ex. om det är viktigt att sökåtgärder är synkroniserade med dynamiska inventeringsdatabaser) är push-modellen ditt enda alternativ.

Den här metoden är mer flexibel än pull-modellen eftersom du kan ladda upp dokument individuellt eller i batchar (upp till 1 000 per batch eller 16 MB, beroende på vilken gräns som nås först). Med push-modellen kan du också ladda upp dokument till Azure Search, oavsett var dina data finns.

### <a name="how-to-push-data-to-an-azure-search-index"></a>Så här skickar du data till ett Azure Search-index

Du kan använda följande API:er för att läsa in ett eller flera dokument i ett index:

+ [Lägg till, uppdatera eller ta bort dokument (REST API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [indexAction-klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) eller [indexBatch-klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Det finns för närvarande inget verktygsstöd för att skicka data via portalen.

En introduktion till varje metod finns i [snabbstarten: Skapa ett Azure Search-index med PowerShell och REST API](search-create-index-rest-api.md) eller [snabbstarten: Skapa ett Azure Search-index i C# ](search-import-data-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Indexering åtgärder: ladda upp, sammanfoga, mergeOrUpload, ta bort

Du kan styra vilken indexeringsåtgärd på basis av per dokument, att ange om dokumentet ska överföras i full, sammanfogade med dokumentinnehåll eller har tagits bort.

Utfärda HTTP POST-förfrågningar i REST-API med JSON-begärandetext till slutpunkts-URL för ditt Azure Search-index. Varje JSON-objekt i matrisen ”value” innehåller dokumentets nyckel och anger en indexeringsåtgärd tilläggs-, uppdateringar, eller tar bort dokumentinnehåll. Finns ett kodexempel i [läsa in dokument](search-create-index-rest-api.md#load-documents).

I .NET SDK, packa upp data till en `IndexBatch` objekt. En `IndexBatch` kapslar in en samling `IndexAction` objekt, vart och ett innehåller ett dokument och en egenskap som meddelar Azure Search vilken åtgärd som ska utföras för dokumentet. Finns ett kodexempel i [konstruera IndexBatch](search-import-data-dotnet.md#construct-indexbatch).


| @search.action | Beskrivning | Nödvändiga fält för varje dokument | Anteckningar |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |En `upload`-åtgärd liknar en ”upsert” där dokumentet infogas om det är nytt och uppdateras/ersätts om det finns. |nyckel plus eventuella andra fält som du vill definiera |När du uppdaterar och ersätter ett befintligt dokument tilldelas alla fält som inte angetts i begäran `null`. Detta sker även om fältet tidigare hade ett värde som inte var null. |
| `merge` |Uppdaterar ett befintligt dokument med de angivna fälten. Sammanfogningen misslyckas om dokumentet inte finns i indexet. |nyckel plus eventuella andra fält som du vill definiera |Alla fält som du anger i en sammanfogning ersätter det befintliga fältet i dokumentet. I .NET SDK, omfattar detta fält av typen `DataType.Collection(DataType.String)`. I REST API, omfattar detta fält av typen `Collection(Edm.String)`. Om dokumentet till exempel innehåller ett `tags`-fält med värdet `["budget"]` och du utför en sammanfogning med värdet `["economy", "pool"]` för `tags` så blir det slutliga värdet för fältet `tags` `["economy", "pool"]`. Det blir inte `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Den här åtgärden fungerar som `merge` om ett dokument med den angivna nyckeln redan finns i indexet. Om dokumentet inte finns fungerar den som `upload` med ett nytt dokument. |nyckel plus eventuella andra fält som du vill definiera |- |
| `delete` |Tar bort det angivna dokumentet från indexet. |endast nyckel |Andra fält som du anger än nyckelfältet ignoreras. Om du vill ta bort ett enstaka fält från ett dokument använder du `merge` i stället och anger bara fältet till null. |

## <a name="decide-which-indexing-action-to-use"></a>Bestäm vilken indexeringsåtgärd som du vill använda
Att importera data med .NET SDK, (ladda upp, sammanfoga, ta bort och mergeOrUpload). Beroende på vilken av åtgärderna nedan som du väljer måste endast vissa fält tas med för varje dokument:


### <a name="formulate-your-query"></a>Formulera frågan
Du kan [söka i ditt index med hjälp av REST-API:et](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) på två sätt. Ett sätt är att skicka en HTTP POST-begäran där dina frågeparametrar definieras i ett JSON-objekt i begärandetexten. Det andra sättet är att skicka en HTTP GET-begäran där dina frågeparametrar definieras i URL:en för begäran. POST har mindre [restriktiva gränser](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) vad gäller frågeparametrarnas storlek än GET. Av den anledningen rekommenderar vi att du använder POST såvida det inte finns särskilda omständigheter som gör att GET är lämpligare.

För både POST och GET måste du ange *tjänstnamnet*, *indexnamnet* och *API-versionen* (den aktuella API-versionen är `2017-11-11` vid tidpunkten för publiceringen av det här dokumentet) i URL:en för begäran. För GET anger du frågeparametrarna i *frågesträngen* i slutet av URL:en. Se URL-formatet nedan:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2017-11-11

Formatet för POST är samma, men med endast ”api-version” i frågesträngsparametrarna.


## <a name="pulling-data-into-an-index"></a>Hämta in data till ett index
Datahämtningsmodellen crawlar en datakälla som stöds och överför automatiskt data till ditt index. I Azure Search har den här funktionen implementerats med hjälp av *indexerare*, som för närvarande finns tillgängliga för följande plattformar:

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Azure SQL Database och SQL Server på virtuella Azure-datorer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Indexerare ansluter ett index till en datakälla (vanligtvis en tabell, vy eller motsvarande struktur) och mappar källfält till motsvarande fält i indexet. Under körningen omvandlas raduppsättningen automatiskt till JSON och läses in i det angivna indexet. Alla indexerare stöder schemaläggning så att du kan ange hur ofta data ska uppdateras. De flesta indexerare tillhandahåller ändringsspårning om datakällan har stöd för det. Indexerare spårar ändringar och borttagningar av befintliga dokument och identifierar nya dokument, vilket gör att du slipper hantera dina data i indexet aktivt. 


### <a name="how-to-pull-data-into-an-azure-search-index"></a>Så här hämtar du in data till ett Azure Search-index

Indexerarfunktioner exponeras på [Azure Portal](search-import-data-portal.md), i [REST-API:et](/rest/api/searchservice/Indexer-operations) och i [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions). 

En fördel med att använda portalen är att Azure Search vanligtvis kan generera ett standardindexschema åt dig genom att läsa källdatauppsättningens metadata. Du kan ändra det genererade indexet tills indexet har bearbetats. Därefter är de enda schemaändringarna som tillåts de som inte kräver omindexering. Om de ändringar som du vill göra påverkar schemat direkt måste indexet återskapas. 

## <a name="verify-data-import-with-search-explorer"></a>Verifiera dataimporten med Sökutforskaren

Ett snabbt sätt att utföra en preliminär kontroll av dokumentöverföringen är att använda **Sökutforskaren** i portalen. Med utforskaren kan du köra frågor mot ett index utan att behöva skriva kod. Sökupplevelse baseras på standardinställningar, t.ex. [enkel syntax](/rest/api/searchservice/simple-query-syntax-in-azure-search) och [searchMode-frågeparametern](/rest/api/searchservice/search-documents). Resultaten returneras i JSON så att du kan gå igenom hela dokumentet.

> [!TIP]
> Många [Azure Search-kodexempel](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) innehåller inbäddade eller lättillgängliga datauppsättningar, som hjälper dig att snabbt komma igång. Portalen innehåller också en exempelindexerare och datakälla som består av en liten datauppsättning med fastighetsinformation (med namnet ”realestate-us-sample”). När du kör den förkonfigurerade indexeraren på datakällan exemplet skapas ett index och fylls med dokument som kan efterfrågas i Sökutforskaren eller med kod som du skriver.

## <a name="see-also"></a>Se också

+ [Översikt över indexeraren](search-indexer-overview.md)
+ [Steg-för-steg-beskrivning av portalen: skapa, läsa in och avfråga ett index](search-get-started-portal.md)

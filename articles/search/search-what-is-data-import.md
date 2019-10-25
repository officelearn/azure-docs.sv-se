---
title: Data import för data inmatning i ett Sök index
titleSuffix: Azure Cognitive Search
description: Fylla i och ladda upp data till ett index i Azure Kognitiv sökning från externa data källor.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a05291012bcf44b1a07d9b451eef1302862b2fce
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794147"
---
# <a name="data-import-overview---azure-cognitive-search"></a>Översikt över data import – Azure Kognitiv sökning

I Azure Kognitiv sökning körs frågor över ditt innehåll som läses in i och sparas i ett [sökindex](search-what-is-an-index.md). Den här artikeln går igenom de två grundläggande metoderna för att fylla ett index: *Skicka* data till indexet program mässigt eller peka en [Azure kognitiv sökning-indexerare](search-indexer-overview.md) på en data källa som stöds för att *Hämta* data.

Med båda metoderna är målet att *läsa in data* från en extern data källa till ett Azure kognitiv sökning-index. Med Azure Kognitiv sökning kan du skapa ett tomt index, men tills du skickar eller hämtar data till den är det inte möjligt att fråga.

## <a name="pushing-data-to-an-index"></a>Skicka data till ett index
Push-modellen, som används för att program mässigt skicka data till Azure Kognitiv sökning, är den mest flexibla metoden. För det första finns det inga begränsningar på vilken typ av datakälla som får användas. Alla data uppsättningar som består av JSON-dokument kan skickas till ett Azure Kognitiv sökning-index, förutsatt att varje dokument i data uppsättningen har fält mappning till fält som definierats i index schemat. För det andra finns det inga begränsningar på körningsfrekvensen. Du kan skicka ändringar till ett index så ofta du vill. För program som har mycket låga fördröjningskrav (t.ex. om det är viktigt att sökåtgärder är synkroniserade med dynamiska inventeringsdatabaser) är push-modellen ditt enda alternativ.

Den här metoden är mer flexibel än pull-modellen eftersom du kan ladda upp dokument individuellt eller i batchar (upp till 1 000 per batch eller 16 MB, beroende på vilken gräns som nås först). Med push-modellen kan du också ladda upp dokument till Azure Kognitiv sökning oavsett var dina data finns.

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>Skicka data till ett Azure Kognitiv sökning-index

Du kan använda följande API:er för att läsa in ett eller flera dokument i ett index:

+ [Lägg till, uppdatera eller ta bort dokument (REST API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [indexAction-klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) eller [indexBatch-klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Det finns för närvarande inget verktygsstöd för att skicka data via portalen.

En introduktion till varje metod finns i [snabb start: skapa ett Azure kognitiv sökning-index med hjälp av PowerShell](search-create-index-rest-api.md) eller [ C# snabb start: skapa ett Azure kognitiv sökning-index med .NET SDK](search-get-started-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Indexerings åtgärder: Ladda upp, sammanfoga, mergeOrUpload, ta bort

Du kan styra vilken typ av indexerings åtgärd som ska användas för varje dokument, ange om dokumentet ska överföras fullständigt, slås samman med befintligt dokument innehåll eller tas bort.

I REST API skickar du HTTP POST-begäranden med JSON-begäranden till URL: en för Azure Kognitiv sökning indexets slut punkt. Varje JSON-objekt i "value"-matrisen innehåller dokumentets nyckel och anger om en indexerings åtgärd lägger till, uppdaterar eller tar bort dokument innehåll. Ett kod exempel finns i [läsa in dokument](search-get-started-dotnet.md#load-documents).

I .NET SDK ska du paketera data i ett `IndexBatch`-objekt. En `IndexBatch` kapslar in en samling `IndexAction` objekt, som var och en innehåller ett dokument och en egenskap som talar om för Azure Kognitiv sökning vilken åtgärd som ska utföras i dokumentet. Ett kod exempel finns i [ C# snabb](search-get-started-dotnet.md)starten.


| @search.action | Beskrivning | Nödvändiga fält för varje dokument | Anteckningar |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |En `upload`-åtgärd liknar en ”upsert” där dokumentet infogas om det är nytt och uppdateras/ersätts om det finns. |nyckel plus eventuella andra fält som du vill definiera |När du uppdaterar och ersätter ett befintligt dokument tilldelas alla fält som inte angetts i begäran `null`. Detta sker även om fältet tidigare hade ett värde som inte var null. |
| `merge` |Uppdaterar ett befintligt dokument med de angivna fälten. Sammanfogningen misslyckas om dokumentet inte finns i indexet. |nyckel plus eventuella andra fält som du vill definiera |Alla fält som du anger i en sammanfogning ersätter det befintliga fältet i dokumentet. I .NET SDK inkluderar detta fält av typen `DataType.Collection(DataType.String)`. I REST API innehåller detta fält av typen `Collection(Edm.String)`. Om dokumentet till exempel innehåller ett `tags`-fält med värdet `["budget"]` och du utför en sammanfogning med värdet `["economy", "pool"]` för `tags` så blir det slutliga värdet för fältet `tags` `["economy", "pool"]`. Det blir inte `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Den här åtgärden fungerar som `merge` om ett dokument med den angivna nyckeln redan finns i indexet. Om dokumentet inte finns fungerar den som `upload` med ett nytt dokument. |nyckel plus eventuella andra fält som du vill definiera |- |
| `delete` |Tar bort det angivna dokumentet från indexet. |endast nyckel |Andra fält som du anger än nyckelfältet ignoreras. Om du vill ta bort ett enstaka fält från ett dokument använder du `merge` i stället och anger bara fältet till null. |

## <a name="decide-which-indexing-action-to-use"></a>Bestäm vilken indexeringsåtgärd som du vill använda
Importera data med hjälp av .NET SDK, (Ladda upp, sammanfoga, ta bort och mergeOrUpload). Beroende på vilken av åtgärderna nedan som du väljer måste endast vissa fält tas med för varje dokument:


### <a name="formulate-your-query"></a>Formulera frågan
Du kan [söka i ditt index med hjälp av REST-API:et](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) på två sätt. Ett sätt är att skicka en HTTP POST-begäran där dina frågeparametrar definieras i ett JSON-objekt i begärandetexten. Det andra sättet är att skicka en HTTP GET-begäran där dina frågeparametrar definieras i URL:en för begäran. POST har mindre [restriktiva gränser](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) vad gäller frågeparametrarnas storlek än GET. Av den anledningen rekommenderar vi att du använder POST såvida det inte finns särskilda omständigheter som gör att GET är lämpligare.

För både POST och GET måste du ange *tjänstnamnet*, *indexnamnet* och *API-versionen* (den aktuella API-versionen är `2019-05-06` vid tidpunkten för publiceringen av det här dokumentet) i URL:en för begäran. För GET anger du frågeparametrarna i *frågesträngen* i slutet av URL:en. Se URL-formatet nedan:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06

Formatet för POST är samma, men med endast ”api-version” i frågesträngsparametrarna.


## <a name="pulling-data-into-an-index"></a>Hämta in data till ett index
Datahämtningsmodellen crawlar en datakälla som stöds och överför automatiskt data till ditt index. I Azure Kognitiv sökning implementeras den här funktionen genom *indexerare*som för närvarande är tillgängliga för dessa plattformar:

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Azure SQL Database och SQL Server på virtuella Azure-datorer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Indexerare ansluter ett index till en datakälla (vanligtvis en tabell, vy eller motsvarande struktur) och mappar källfält till motsvarande fält i indexet. Under körningen omvandlas raduppsättningen automatiskt till JSON och läses in i det angivna indexet. Alla indexerare stöder schemaläggning så att du kan ange hur ofta data ska uppdateras. De flesta indexerare tillhandahåller ändringsspårning om datakällan har stöd för det. Indexerare spårar ändringar och borttagningar av befintliga dokument och identifierar nya dokument, vilket gör att du slipper hantera dina data i indexet aktivt. 


### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>Så här hämtar du data till ett Azure Kognitiv sökning-index

Indexerarfunktioner exponeras på [Azure Portal](search-import-data-portal.md), i [REST-API:et](/rest/api/searchservice/Indexer-operations) och i [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions). 

En fördel med att använda portalen är att Azure Kognitiv sökning vanligt vis kan generera ett standard index schema åt dig genom att läsa metadata för käll data uppsättningen. Du kan ändra det genererade indexet tills indexet har bearbetats. Därefter är de enda schemaändringarna som tillåts de som inte kräver omindexering. Om de ändringar som du vill göra påverkar schemat direkt måste indexet återskapas. 

## <a name="verify-data-import-with-search-explorer"></a>Verifiera data import med Sök Utforskaren

Ett snabbt sätt att utföra en preliminär kontroll av dokument överföringen är att använda **Sök Utforskaren** i portalen. Med utforskaren kan du köra frågor mot ett index utan att behöva skriva kod. Sökupplevelse baseras på standardinställningar, t.ex. [enkel syntax](/rest/api/searchservice/simple-query-syntax-in-azure-search) och [searchMode-frågeparametern](/rest/api/searchservice/search-documents). Resultaten returneras i JSON så att du kan gå igenom hela dokumentet.

> [!TIP]
> Många [exempel på Azure kognitiv sökning-kod](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) är inbäddade eller lättillgängliga data uppsättningar, vilket ger ett enkelt sätt att komma igång. Portalen innehåller också en exempelindexerare och datakälla som består av en liten datauppsättning med fastighetsinformation (med namnet ”realestate-us-sample”). När du kör den förkonfigurerade indexeraren i exempel data källan skapas ett index som läses in med dokument som sedan kan frågas i Sök Utforskaren eller efter kod som du skriver.

## <a name="see-also"></a>Se också

+ [Översikt över indexeraren](search-indexer-overview.md)
+ [Steg-för-steg-beskrivning av portalen: skapa, läsa in och avfråga ett index](search-get-started-portal.md)

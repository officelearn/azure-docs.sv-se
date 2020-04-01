---
title: 'Självstudiekurs: Indexera halvstrukturerade data i JSON-blobbar'
titleSuffix: Azure Cognitive Search
description: Lär dig hur du indexerar och söker efter halvstrukturerade Azure JSON-blobbar med Azure Cognitive Search REST API:er och Postman.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: ce3b3839319de38020b968ff8db1ee6713b29c47
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78269980"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>Självstudiekurs: Index JSON-blobbar från Azure Storage med REST

Azure Cognitive Search kan indexera JSON-dokument och matriser i Azure blob storage med hjälp av en [indexerare](search-indexer-overview.md) som vet hur man läser halvstrukturerade data. Halvstrukturerade data innehåller taggar eller märkord som separerar innehållet i data. Den delar skillnaden mellan ostrukturerade data, som måste indexeras helt, och formellt strukturerade data som följer en datamodell, till exempel ett relationsdatabasschema, som kan indexeras per fält.

Den här självstudien använder Postman och [REST-API:erna för sökning](https://docs.microsoft.com/rest/api/searchservice/) för att utföra följande uppgifter:

> [!div class="checklist"]
> * Konfigurera en Azure Cognitive Search-datakälla för en Azure-blob-behållare
> * Skapa ett Azure Cognitive Search-index för att innehålla sökbart innehåll
> * Konfigurera och köra en indexerare för att läsa behållaren och extrahera sökbart innehåll från Azure-bloblagring
> * Söka i indexet som du precis skapade

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

+ [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Skrivbordsappen Postman](https://www.getpostman.com/)
+ [Skapa](search-create-service-portal.md) eller [hitta en befintlig söktjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Du kan använda den kostnadsfria tjänsten för den här självstudien. En kostnadsfri söktjänst begränsar dig till tre index, tre indexerare och tre datakällor. I den här kursen skapar du en av varje. Innan du börjar, se till att du har utrymme på din tjänst för att acceptera de nya resurserna.

## <a name="download-files"></a>Hämta filer

[Kliniska prövningar-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) innehåller de data som används i den här guiden. Ladda ner och packa upp den här filen till sin egen mapp. Data kommer från [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results), konverteras till JSON för den här självstudien.

## <a name="1---create-services"></a>1 - Skapa tjänster

Den här självstudien använder Azure Cognitive Search för indexering och frågor och Azure Blob-lagring för att tillhandahålla data. 

Om möjligt, skapa både i samma region och resursgrupp för närhet och hanterbarhet. I praktiken kan ditt Azure Storage-konto finnas i alla regioner.

### <a name="start-with-azure-storage"></a>Börja med Azure Storage

1. [Logga in på Azure-portalen](https://portal.azure.com/) och klicka på **+ Skapa resurs**.

1. Sök efter *lagringskonto* och välj Microsofts erbjudande om lagringskonto.

   ![Skapa lagringskonto](media/cognitive-search-tutorial-blob/storage-account.png "Skapa lagringskonto")

1. På fliken Grunderna krävs följande objekt. Acceptera standardvärdena för allt annat.

   + **Resursgrupp**. Välj en befintlig eller skapa en ny, men använd samma grupp för alla tjänster så att du kan hantera dem kollektivt.

   + **Namn på lagringskonto**. Om du tror att du har flera resurser av samma typ använder du namnet för att skilja efter typ och region, till exempel *blobstoragewestus*. 

   + **Plats**. Om möjligt väljer du samma plats som används för Azure Cognitive Search och Cognitive Services. En enda plats annullerar bandbreddsavgifter.

   + **Konto Typ**. Välj standard, *StorageV2 (allmänt ändamål v2)*.

1. Klicka på **Granska + Skapa** om du vill skapa tjänsten.

1. När den har skapats klickar du på **Gå till resursen** för att öppna sidan Översikt.

1. Klicka på **Blobbar-tjänsten.**

1. [Skapa en Blob-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) som innehåller exempeldata. Du kan ange den offentliga åtkomstnivån till något av dess giltiga värden.

1. När behållaren har skapats öppnar du den och väljer **Ladda upp** i kommandofältet.

   ![Ladda upp i kommandofältet](media/search-semi-structured-data/upload-command-bar.png "Ladda upp i kommandofältet")

1. Navigera till mappen som innehåller exempelfilerna. Markera dem alla och klicka sedan på **Ladda upp**.

   ![Överföra filer](media/search-semi-structured-data/clinicalupload.png "Överföra filer")

När överföringen är klar ska filerna visas i en egen undermapp i datacontainern.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Nästa resurs är Azure Cognitive Search, som du kan [skapa i portalen](search-create-service-portal.md). Du kan använda den kostnadsfria nivån för att slutföra den här genomgången. 

Precis som med Azure Blob-lagring tar du en stund att samla in åtkomstnyckeln. Vidare, när du börjar strukturera begäranden, måste du ange slutpunkten och admin api-nyckeln som används för att autentisera varje begäran.

### <a name="get-a-key-and-url"></a>Hämta en nyckel och webbadress

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En söktjänst skapas med båda, så om du har lagt till Azure Cognitive Search i din prenumeration följer du dessa steg för att få nödvändig information:

1. [Logga in på Azure-portalen](https://portal.azure.com/)och hämta webbadressen i söktjänstens **översiktssida.** Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningsnycklar** > **Keys**får du en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara admin nycklar, som tillhandahålls för kontinuitet i verksamheten om du behöver rulla en över. Du kan använda antingen primär- eller sekundärnyckeln på begäranden om att lägga till, ändra och ta bort objekt.

![Hämta en HTTP-slutpunkt och åtkomstnyckel](media/search-get-started-postman/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomstnyckel")

Alla begäranden kräver en api-nyckel på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="2---set-up-postman"></a>2 - Ställ in Brevbärare

Starta Postman och konfigurera en HTTP-begäran. Om du inte känner till det här verktyget läser du [Utforska Azure Cognitive Search REST API:er med Postman](search-get-started-postman.md).

Begäran metoder för varje samtal i den här guiden är **POST** och **FÅ**. Du ska ringa tre API-anrop till söktjänsten för att skapa en datakälla, ett index och en indexerare. Datakällan innehåller en pekare till ditt lagringskonto och dina JSON-data. Din söktjänst gör anslutningen vid inläsning av data.

I Rubriker anger du "Innehållstyp" till `application/json` och anger `api-key` till administratörs-api-nyckeln för din Azure Cognitive Search-tjänst. När du har angett rubrikerna kan du använda dem för varje begäran i den här övningen.

  ![URL och rubrik för postmanbegäran](media/search-get-started-postman/postman-url.png "URL och rubrik för postmanbegäran")

Uri:er måste ange en api-version och varje anrop ska returnera en **201 Skapad**. Den allmänt tillgängliga api-versionen för att `2019-05-06`använda JSON-matriser är .

## <a name="3---create-a-data-source"></a>3 - Skapa en datakälla

[Api:et skapa datakälla](https://docs.microsoft.com/rest/api/searchservice/create-data-source) skapar ett Azure Cognitive Search-objekt som anger vilka data som ska indexeras.

1. Ställ in slutpunkten för `https://[service name].search.windows.net/datasources?api-version=2019-05-06`det här anropet på . Ersätt `[service name]` med namnet på söktjänsten. 

1. Kopiera följande JSON till begäran kroppen.

    ```json
    {
        "name" : "clinical-trials-json-ds",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
        "container" : { "name" : "[blob container name]"}
    }
    ```

1. Ersätt anslutningssträngen med en giltig sträng för ditt konto.

1. Ersätt "[blob container name]" med behållaren som du skapade för exempeldata. 

1. Skicka begäran. Svaret ska se ut så här:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
        "@odata.etag": "\"0x8D505FBC3856C9E\"",
        "name": "clinical-trials-json-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
        },
        "container": {
            "name": "[mycontainernamehere]",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

## <a name="4---create-an-index"></a>4 - Skapa ett index
    
Det andra anropet är [Skapa index-API](https://docs.microsoft.com/rest/api/searchservice/create-index), skapa ett Azure Cognitive Search-index som lagrar alla sökbara data. Ett index anger alla parametrar och deras attribut.

1. Ställ in slutpunkten för `https://[service name].search.windows.net/indexes?api-version=2019-05-06`det här anropet på . Ersätt `[service name]` med namnet på söktjänsten.

1. Kopiera följande JSON till begäran kroppen.

    ```json
    {
      "name": "clinical-trials-json-index",  
      "fields": [
      {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
      {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
      {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
      {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
      {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
      {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
      {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
      {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
      {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
      {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
      {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
      {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
      {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
      {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
      {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
      ]
    }
   ```

1. Skicka begäran. Svaret ska se ut så här:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag": "\"0x8D505FC00EDD5FA\"",
        "name": "clinical-trials-json-index",
        "fields": [
            {
                "name": "FileName",
                "type": "Edm.String",
                "searchable": false,
                "filterable": false,
                "retrievable": true,
                "sortable": true,
                "facetable": false,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            {
                "name": "Description",
                "type": "Edm.String",
                "searchable": true,
                "filterable": false,
                "retrievable": false,
                "sortable": false,
                "facetable": false,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            ...
          }
    ```

## <a name="5---create-and-run-an-indexer"></a>5 - Skapa och kör en indexerare

En indexerare ansluter till datakällan, importerar data till målsökindexet och ger eventuellt ett schema för att automatisera datauppdateringen. REST API är [Skapa Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

1. Ställ in URI:n `https://[service name].search.windows.net/indexers?api-version=2019-05-06`för det här anropet på . Ersätt `[service name]` med namnet på söktjänsten.

1. Kopiera följande JSON till begäran kroppen.

    ```json
    {
      "name" : "clinical-trials-json-indexer",
      "dataSourceName" : "clinical-trials-json-ds",
      "targetIndexName" : "clinical-trials-json-index",
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
    ```

1. Skicka begäran. Begäran behandlas omedelbart. När svaret kommer tillbaka har du ett index som är fulltextsökbart. Svaret ska se ut så här:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
        "@odata.etag": "\"0x8D505FDE143D164\"",
        "name": "clinical-trials-json-indexer",
        "description": null,
        "dataSourceName": "clinical-trials-json-ds",
        "targetIndexName": "clinical-trials-json-index",
        "schedule": null,
        "parameters": {
            "batchSize": null,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonArray"
            }
        },
        "fieldMappings": [],
        "enrichers": [],
        "disabled": null
    }
    ```

## <a name="6---search-your-json-files"></a>6 - Sök dina JSON-filer

Du kan börja söka så fort det första dokumentet läses in.

1. Ändra verbet till **GET**.

1. Ställ in URI:n `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&api-version=2019-05-06&$count=true`för det här anropet på . Ersätt `[service name]` med namnet på söktjänsten.

1. Skicka begäran. Det här är en ospecificerad fulltextsökfråga som returnerar alla fält som markerats som hämtningsbara i indexet, tillsammans med ett antal dokument. Svaret ska se ut så här:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/indexes('clinical-trials-json-index')/$metadata#docs(*)",
        "@odata.count": 100,
        "value": [
            {
                "@search.score": 1.0,
                "FileName": "NCT00000102.txt",
                "MinimumAge": 14,
                "Title": "Congenital Adrenal Hyperplasia: Calcium Channels as Therapeutic Targets",
                "MyURL": "https://azure.storagedemos.com/clinical-trials/NCT00000102.txt",
                "Gender": "Both",
                "MaximumAge": 35,
                "Summary": "This study will test the ability of extended release nifedipine (Procardia XL), a blood pressure medication, to permit a decrease in the dose of glucocorticoid medication children take to treat congenital adrenal hyperplasia (CAH).",
                "NCTID": "NCT00000102",
                "Phase": "Phase 1/Phase 2",
                "Date": "ClinicalTrials.gov processed this data on October 25, 2016",
                "OverallStatus": "Completed",
                "OrgStudyId": "NCRR-M01RR01070-0506",
                "HealthyVolunteers": "No",
                "Keywords": [],
                "metadata_storage_last_modified": "2019-04-09T18:16:24Z",
                "metadata_storage_size": "33060",
                "metadata_content_type": null
            },
            . . . 
    ```

1. Lägg `$select` till frågeparametern för att `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&$select=Gender,metadata_storage_size&api-version=2019-05-06&$count=true`begränsa resultatet till färre fält: .  För den här frågan matchar 100 dokument, men som standard returnerar Azure Cognitive Search bara 50 i resultaten.

   ![Parametriserad fråga](media/search-semi-structured-data/lastquery.png "Paramteriserad fråga")

1. Ett exempel på mer `$filter=MinimumAge ge 30 and MaximumAge lt 75`komplex fråga skulle vara , som returnerar endast resultat där parametrarna MinimumAge är större än eller lika med 30 och MaximumAge är mindre än 75. Ersätt `$select` uttrycket `$filter` med uttrycket.

   ![Halvstrukturerad sökning](media/search-semi-structured-data/metadatashort.png)

Du kan också använda logiska operatorer (och, eller, inte) och jämförelseoperatorer (eq, ne, gt, lt, ge, le). Strängjämförelser är skiftlägeskänsliga. Mer information och exempel finns i [Skapa en enkel fråga](search-query-simple-examples.md).

> [!NOTE]
> Parametern `$filter` fungerar endast med metadata som markerades som filtrerbara när indexet skapades.

## <a name="reset-and-rerun"></a>Återställa och köra igen

I de tidiga experimentella utvecklingsstadierna är den mest praktiska metoden för designiteration att ta bort objekten från Azure Cognitive Search och låta koden återskapa dem. Resursnamn är unika. Om du tar bort ett objekt kan du återskapa det med samma namn.

Du kan använda portalen för att ta bort index, indexerare och datakällor. Eller använd **DELETE** och ange webbadresser till varje objekt. Följande kommando tar bort en indexerare.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2019-05-06
```

Statuskod 204 returneras vid borttagning.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration är det i slutet av ett projekt en bra idé att ta bort de resurser som du inte längre behöver. Resurser som fortsätter att köras kan medföra kostnader. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken Alla resurser eller Resursgrupper i fönstret för vänsternavigering.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med grunderna i Azure Blob-indexering, låt oss ta en närmare titt på indexerkonfiguration för JSON-blobbar i Azure Storage.

> [!div class="nextstepaction"]
> [Konfigurera JSON-blobindexering](search-howto-index-json-blobs.md)
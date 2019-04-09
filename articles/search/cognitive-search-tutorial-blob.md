---
title: 'Självstudier: Anropa API: er med Cognitive Services i en pipeline för fulltextindexering – Azure Search'
description: Gå igenom ett exempel på extrahering av data, naturligt språk och bilder AI bearbetning i Azure Search indexering för extrahering av data och transformering över JSON-blobar.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 5fbcef1d8bc19df251a4d33cafa2fa7b5a7d9431
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59261929"
---
# <a name="tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline-preview"></a>Självstudier: Anropa API: er med Cognitive Services i ett Azure Search indexering pipeline (förhandsversion)

I den här självstudien har du lärt dig mekaniken bakom att programmera databerikande i Azure Search med *kognitiva kunskaper*. Kunskaper backas upp av språkbearbetning (NLP) och avbildning analysmöjligheter i Cognitive Services. Via kompetens sammansättning och konfiguration, för att extrahera text och text som representerar en bild eller skannade dokument. Du kan också identifiera språk, entiteter, nyckelfraser och mycket mer. Slutresultatet är omfattande ytterligare innehåll i ett Azure Search-index som skapats av en AI-driven indexering av pipeline. 

I den här självstudien gör du REST API-anrop för att utföra följande uppgifter:

> [!div class="checklist"]
> * Skapa en indexeringspipeline som berikar källdata på väg till ett index
> * Använd inbyggda färdigheter: entitetsigenkänning, språkidentifiering, textredigering och extrahering av viktiga fraser
> * Lära dig att sammanlänka kunskaper genom att mappa indata till utdata i en kompetens
> * Köra begäranden och granska resultatet
> * Återställa index och indexerare för ytterligare utveckling

Utdata är ett fulltextsökbart index i Azure Search. Du kan förbättra indexet med andra standardfunktioner som [synonymer](search-synonyms.md), [bedömningsprofiler](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analysverktyg](search-analyzers.md) och [filter](search-filters.md).

Den här självstudien körs på den kostnadsfria tjänsten, men antalet kostnadsfria transaktioner är begränsat till 20 dokument per dag. Om du vill köra den här självstudien mer än en gång i samma dag, kan du använda en mindre fil så att du kan rymmas i fler körningar.

> [!NOTE]
> När du expanderar omfång genom att öka frekvensen för bearbetning, lägga till fler dokument eller att lägga till fler AI-algoritmer, behöver du bifoga en fakturerbar resurs för Cognitive Services. Avgifter tillkommer när du anropar API: er i Cognitive Services och extrahering av avbildningen som en del av det dokumentknäckning steget i Azure Search. Det finns inga avgifter för textextrahering från dokument.
>
> Körningen av inbyggda färdigheter som ingår debiteras enligt den befintliga [Cognitive Services betala-som-du gå pris](https://azure.microsoft.com/pricing/details/cognitive-services/) . Bild extrahering priser som ingår debiteras enligt pris för förhandsversion, enligt beskrivningen på den [Azure Search sidan med priser](https://go.microsoft.com/fwlink/?linkid=2042400). Läs [mer](cognitive-search-attach-cognitive-services.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

[Skapa en Azure Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här självstudiekursen.

[Skrivbordsappen postman](https://www.getpostman.com/) används för att göra REST-anrop till Azure Search.

### <a name="get-an-azure-search-api-key-and-endpoint"></a>Hitta en Azure Search api-nyckel och slutpunkt

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En söktjänst har vanligen båda dessa komponenter, så om du har valt att lägga till Azure Search i din prenumeration följer du bara stegen nedan för att hitta fram till rätt information:

1. Azure-portalen i din söktjänst **översikt** sidan, hämta URL: en. Här följer ett exempel på hur en slutpunkt kan se ut: `https://my-service-name.search.windows.net`.

2. I **inställningar** > **nycklar**, hämta en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara administratörsnycklar, som angetts för kontinuitet för företag om du behöver förnya ett. Du kan använda antingen den primära eller sekundära nyckeln för förfrågningar för att lägga till, ändra och ta bort objekt.

![Hämta en HTTP-slutpunkt och åtkomstnyckel](media/search-fiddler/get-url-key.png "får en HTTP-slutpunkt och åtkomstnyckel")

Alla begäranden som kräver en api-nyckel för varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

### <a name="set-up-azure-blob-service-and-load-sample-data"></a>Konfigurera Azure Blob Service och läsa in exempeldata

Berikningspipelinen hämtar data från Azure-datakällor. Källdata måste komma från en datakällstyp som stöds av en [Azure Search-indexerare](search-indexer-overview.md). Observera att Azure Table Storage inte stöds för kognitiv sökning. I den här övningen använder vi blogglagring för att demonstrera flera typer av innehåll.

1. [Ladda ned exempeldata](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) som består av en liten filuppsättning med olika typer av data. 

1. [Registrera dig för Azure Blob storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal), skapa ett lagringskonto, öppna Blob tjänster sidor och skapa en behållare. Skapa lagringskontot i samma region som Azure Search.

1. I den container du skapade klickar du på **Ladda upp** för att ladda upp de exempelfiler som du laddade ned i ett tidigare steg.

   ![Källfiler i Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. När exempelfilerna har lästs in hämtar du containerns namn och en anslutningssträng för Blob Storage. Det kan du göra genom att gå till lagringskontot i Azure Portal. Gå till **Åtkomstnycklar** och kopiera fältet **Anslutningssträng**.

   Anslutningssträngen ska vara en URL som ser ut ungefär så här:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Det finns andra sätt att ange anslutningssträngen, till exempel att ange en signatur för delad åtkomst. Om du vill veta mer om autentiseringsuppgifter för datakällor kan du läsa [Indexing Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#Credentials) (Indexera Azure Blob Storage).

## <a name="create-a-data-source"></a>Skapa en datakälla

Nu när dina tjänster och källfiler är förberedda kan du börja samla in komponenterna för din indexeringspipeline. Börja med ett [datakällobjekt](https://docs.microsoft.com/rest/api/searchservice/create-data-source) som talar om för Azure Search hur externa källdata ska hämtas.

För den här självstudien använder du REST API och ett verktyg som kan formulera och skicka HTTP-förfrågningar som PowerShell, Postman eller Fiddler. I begärandehuvudet anger du tjänstnamnet du använde när du skapade Azure Search-tjänsten, och API-nyckeln du genererade för din söktjänst. Ange blobcontainern och anslutningssträngen i frågans brödtext.

### <a name="sample-request"></a>Exempelförfrågan
```http
POST https://[service name].search.windows.net/datasources?api-version=2017-11-11-Preview
Content-Type: application/json
api-key: [admin key]
```
#### <a name="request-body-syntax"></a>Begärandetextsyntax
```json
{
  "name" : "demodata",
  "description" : "Demo files to demonstrate cognitive search capabilities.",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" :
    "DefaultEndpointsProtocol=https;AccountName=<your account name>;AccountKey=<your account key>;"
  },
  "container" : { "name" : "<your blob container name>" }
}
```
Skicka begäran. Webbtestverktyget bör returnera en statuskod på 201 vilket bekräftar att det lyckats. 

Eftersom detta var din första begäran ska du kolla Azure-portalen för att bekräfta att datakällan skapades i Azure Search. På söktjänstens instrumentpanelsida verifierar du att panelen Datakällor har ett nytt objekt. Du kan behöva vänta några minuter medan portalsidan uppdateras. 

  ![Datakällspanel i portalen](./media/cognitive-search-tutorial-blob/data-source-tile.png "Datakällspanel i portalen")

Om du ser felet 403 eller 404 ska du kontrollera konstruktionen för begäran: `api-version=2017-11-11-Preview` ska vara på slutpunkten, `api-key` ska vara i rubriken efter `Content-Type` och dess värde måste vara giltigt för en söktjänst. Du kan återanvända rubriken för de kvarvarande stegen i den här självstudien.

## <a name="create-a-skillset"></a>Skapa en kunskapsuppsättning

I det här steget definierar du en uppsättning med berikningssteg som du vill använda för dina data. Du kallar varje berikande steg för en *kunskap*, och uppsättningen med berikande steg för en *kunskapsuppsättning*. Den här självstudien använder [fördefinierade kognitiva kunskaper](cognitive-search-predefined-skills.md) för kunskapsuppsättningen:

+ [Språkidentifiering](cognitive-search-skill-language-detection.md) för att identifiera innehållets språk.

+ [Textuppdelning](cognitive-search-skill-textsplit.md) för att dela upp stort innehåll i mindre delar innan du anropar färdigheten extrahering av nyckelfraser. Extrahering av nyckelfraser accepterar indata på 50 000 tecken eller mindre. Några av exempelfilerna måste delas upp för att rymmas inom gränsen.

+ [Igenkänning av namngiven enhet](cognitive-search-skill-named-entity-recognition.md) för extrahering av namnen och organisationerna från innehåll i blobcontainern.

+ [Extrahering av nyckelfraser](cognitive-search-skill-keyphrases.md) för att hämta viktigaste nyckelfraserna. 

### <a name="sample-request"></a>Exempelförfrågan
Innan du gör det här REST-anropet ska du komma ihåg att ersätta tjänstens namn och administrationsnyckeln i begäran nedan om ditt verktyg inte bevarar begärandehuvudet mellan anrop. 

Denna begäran skapar en kunskapsuppsättning. Hänvisa till kunskapsuppsättningsnamnet ```demoskillset``` för resten av självstudien.

```http
PUT https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Begärandetextsyntax
```json
{
  "description":
  "Extract entities, detect language and extract key-phrases",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text", "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations", "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
      "inputs": [
        {
          "name": "text", "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "languageCode",
          "targetName": "languageCode"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
      "textSplitMode" : "pages",
      "maximumPageLength": 4000,
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        },
        {
          "name": "languageCode",
          "source": "/document/languageCode"
        }
      ],
      "outputs": [
        {
          "name": "textItems",
          "targetName": "pages"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
      "context": "/document/pages/*",
      "inputs": [
        {
          "name": "text", "source": "/document/pages/*"
        },
        {
          "name":"languageCode", "source": "/document/languageCode"
        }
      ],
      "outputs": [
        {
          "name": "keyPhrases",
          "targetName": "keyPhrases"
        }
      ]
    }
  ]
}
```

Skicka begäran. Webbtestverktyget bör returnera en statuskod på 201 vilket bekräftar att det lyckats. 

#### <a name="about-the-request"></a>Om begäran

Lägg märke till hur kunskapen för nyckelfrasextrahering används för varje sida. Genom att ange kontexten ```"document/pages/*"``` kör du den här berikaren för varje medlem i dokument-/sidmatrisen (för varje sida i dokumentet).

Varje kunskap körs på innehållet i dokumentet. Under bearbetningen delar Azure Search upp varje dokument för att läsa innehåll från olika filformat. Text som hittas från källfilen placeras i ett genererat ```content```-fält, ett för varje dokument. Ange indata som ```"/document/content"```.

En grafisk representation av kunskapsuppsättningen visas nedan. 

![Förstå en kunskapsuppsättning](media/cognitive-search-tutorial-blob/skillset.png "Förstå en kunskapsuppsättning")

Utdata kan mappas till ett index som används som indata till en underordnad kunskap, eller både, vilket är fallet med språkkod. I indexet kan en språkkod användas för filtrering. Som indata används språkkoden av textanalyskunskaper för att informera om de språkliga reglerna kring ordnedbrytning.

Mer information om grunderna i kunskapsuppsättningar finns i [Definiera en kunskapsuppsättning](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Skapa ett index

I det här avsnittet definierar du indexschemat genom att ange vilka fält som ska ingå i det sökbara indexet och sökattributen för varje fält. Fält har en typ och kan ta attribut som bestämmer hur fältet ska användas (sökbart, sorteringsbart och så vidare). Fältnamn i ett index krävs inte för att matcha fältnamn identiskt i källan. I ett senare steg lägger du till fältmappningar i en indexerare för att ansluta källa-mål-fält. För det här steget definiera indexet med fältnamnkonventioner som är relevanta för ditt sökprogram.

Den här övningen använder följande fält och fälttyp:

| fält-namn: | `id`       | innehåll   | languageCode | keyPhrases         | organisationer     |
|--------------|----------|-------|----------|--------------------|-------------------|
| fält-typer: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="sample-request"></a>Exempelförfrågan
Innan du gör det här REST-anropet ska du komma ihåg att ersätta tjänstens namn och administrationsnyckeln i begäran nedan om ditt verktyg inte bevarar begärandehuvudet mellan anrop. 

Denna begäran skapar ett index. Använd indexnamnet ```demoindex``` för resten av självstudien.

```http
PUT https://[servicename].search.windows.net/indexes/demoindex?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Begärandetextsyntax

```json
{
  "fields": [
    {
      "name": "id",
      "type": "Edm.String",
      "key": true,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "sortable": true
    },
    {
      "name": "content",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "languageCode",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "keyPhrases",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "organizations",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```
Skicka begäran. Webbtestverktyget bör returnera en statuskod på 201 vilket bekräftar att det lyckats. 

Om du vill veta mer om att definiera ett index kan du läsa [Create Index (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Skapa index (Azure Search-REST API)).


## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Skapa en indexerare, mappa fält och köra transformeringar

Hittills har du skapat en datakälla, en kunskapsuppsättning och ett index. De här tre komponenterna blir en del av en [indexerare](search-indexer-overview.md) som sammanför varje del till en enda åtgärd i flera faser. Om du vill sammanfoga dem i en indexerare måste du definiera fältmappningar. Fältmappningar är en del av definitionen och kör transformationerna när du skickar förfrågan.

För icke-berikad indexering anger indexerardefinitionen ett valfritt *fieldMappings*-avsnitt om fältnamnen eller datatyperna inte matchar exakt, eller om du vill använda en funktion.

För kognitiva sökarbetsbelastningar med en berikande pipeline kräver en indexerare *outputFieldMappings*. Dessa mappningar används när en intern process (berikande pipeline) är källan till fältvärden. Beteenden som är unika för *outputFieldMappings* inkluderar möjligheten att hantera komplexa typer som skapas som en del av berikningen (via formarkunskapen). Det kan även finnas många element per dokument (till exempel flera organisationer i ett dokument). Konstruktionen *outputFieldMappings* kan dirigera systemet för att ”platta ut” samlingar med element i en enskild post.

### <a name="sample-request"></a>Exempelförfrågan

Innan du gör det här REST-anropet ska du komma ihåg att ersätta tjänstens namn och administrationsnyckeln i begäran nedan om ditt verktyg inte bevarar begärandehuvudet mellan anrop. 

Ange även namnet på indexeraren. Du kan hänvisa till det som ```demoindexer``` för resten av den här självstudien.

```http
PUT https://[servicename].search.windows.net/indexers/demoindexer?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Begärandetextsyntax

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
  "fieldMappings" : [
    {
      "sourceFieldName" : "metadata_storage_path",
      "targetFieldName" : "id",
      "mappingFunction" :
        { "name" : "base64Encode" }
    },
    {
      "sourceFieldName" : "content",
      "targetFieldName" : "content"
    }
  ],
  "outputFieldMappings" :
  [
    {
      "sourceFieldName" : "/document/organizations",
      "targetFieldName" : "organizations"
    },
    {
      "sourceFieldName" : "/document/pages/*/keyPhrases/*",
      "targetFieldName" : "keyPhrases"
    },
    {
      "sourceFieldName": "/document/languageCode",
      "targetFieldName": "languageCode"
    }
  ],
  "parameters":
  {
    "maxFailedItems":-1,
    "maxFailedItemsPerBatch":-1,
    "configuration":
    {
      "dataToExtract": "contentAndMetadata",
      "imageAction": "generateNormalizedImages"
    }
  }
}
```

Skicka begäran. Webbtestverktyget bör returnera en statuskod på 201 vilket bekräftar bearbetningen. 

Förvänta dig att steget kan ta flera minuter att slutföra. Trots att datauppsättningen är liten är analytiska kunskaper beräkningsintensiva. Vissa kunskaper, som bildanalys, är tidskrävande.

> [!TIP]
> När en indexerare skapas anropas pipelinen. Om det uppstår problem med att ansluta till data, mappningsindata eller -utdata eller ordningen på åtgärder visas dem i det här stadiet. Du kan behöva ta bort objekt först om du vill köra pipelinen med kod- eller skriptändringar. Mer information finns i [Reset and re-run](#reset) (Återställa och köra om).

### <a name="explore-the-request-body"></a>Utforska begärantexten

Skriptet ställer in ```"maxFailedItems"```  på -1, vilket instruerar indexeringsmotorn att ignorera fel under dataimport. Detta är användbart eftersom det finns det så få dokument i demo-datakällan. För en större datakälla skulle du ställa in värdet på större än 0.

Observera också ```"dataToExtract":"contentAndMetadata"```-instruktionen i konfigurationsparametrarna. Den här instruktionen anger att indexeraren automatiskt ska extrahera innehållet från olika filformat samt metadata som är relaterade till varje fil. 

När innehållet har extraherats kan du ställa in ```imageAction``` på att extrahera text från avbildningar som hittades i datakällan. Konfigurationen av ```"imageAction":"generateNormalizedImages"```, tillsammans med OCR-färdigheten och färdigheten för textsammanslagning, talar om för indexeraren att den ska extrahera text från bilderna (exempelvis ordet ”stopp” från en trafikstoppskylt) och bädda in den som en del av innehållsfältet. Det här beteendet gäller både avbildningarna som är inbäddade i dokumenten (tänk på en avbildning i en PDF) samt avbildningar som hittas i datakällan, till exempel en JPG-fil.

## <a name="check-indexer-status"></a>Kontrollera status för indexerare

När du har definierat indexeraren körs den automatiskt när du skickar din begäran. Beroende på vilka kognitiva kunskaper du har definierat kan indexeringen ta längre tid än väntat. Skicka följande begäran för att kontrollera indexerarens status om du vill ta reda på om indexeraren fortfarande körs.

```http
GET https://[servicename].search.windows.net/indexers/demoindexer/status?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

Svaret anger om indexeraren körs. När indexeringen är klar använder du en annan HTTP GET till STATUS-slutpunkten (enligt ovan) för att se rapporter för eventuella fel och varningar som uppstod under berikandet.  

Varningar är vanliga med vissa källfils- och kunskapskombinationer och är inte alltid tecken på problem. I den här självstudien är varningarna ofarliga (till exempel inga textindata från JPEG-filerna). Du kan granska statussvaret om du vill ha utförlig information om varningar som har genererats under indexeringen.
 
## <a name="verify-content"></a>Kontrollera innehåll

När indexeringen är klar kör du frågor som returnerar innehållet i enskilda fält. Som standard returnerar Azure Search de 50 bästa resultaten. Exempeldata är små, så standardinställningen fungerar gott och väl. Men när du arbetar med större datamängder kanske du måste inkludera parametrar i frågesträngen för att returnera fler resultat. Instruktioner finns i [How to page results in Azure Search](search-pagination-page-layout.md) (Söka resultat i Azure Search).

Som ett verifieringssteg ska du fråga indexet för alla fält.

```http
GET https://[servicename].search.windows.net/indexes/demoindex?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

Utdata är indexeringsschema med namn, typ och attribut för varje fält.

Skicka en ny fråga för `"*"` för att returnera hela innehållet i ett enda fält som `organizations`.

```http
GET https://[servicename].search.windows.net/indexes/demoindex/docs?search=*&$select=organizations&api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

Upprepa detta för ytterligare fält: innehåll, languageCode, keyPhrases och organisationer i den här övningen. Du kan returnera flera fält via `$select` med hjälp av en kommaavgränsad lista.

Du kan använda GET eller POST, beroende på frågesträngens komplexitet och längd. Mer information finns i [Query using the REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Fråga med REST API).

<a name="access-enriched-document"></a>

## <a name="accessing-the-enriched-document"></a>Åtkomst till det utökade dokumentet

Med kognitiv sökning kan du se strukturen för det berikade dokumentet. Berikade dokument är tillfälliga strukturer som skapas under berikandet. De tas sedan bort när processen är klar.

Om du vill ta en ögonblicksbild av det berikade dokumentet som skapades under indexeringen lägger du till ett fält som heter ```enriched``` i ditt index. Indexeraren placerar automatiskt en strängrepresentation i fältet för alla berikanden för det dokumentet.

Fältet ```enriched``` innehåller en sträng som är en logisk representation av det berikade dokumentet i minnet i JSON.  Fältets värde är emellertid ett giltigt JSON-dokument. Kvoter är undantagna, så du behöver aldrig ersätta `\"` med `"` för att visa dokumenten som formaterad JSON.  

Fältet ```enriched``` är avsett för felsökning, endast för att hjälpa dig att förstå innehållets logiska form som uttryck utvärderas mot. Det kan vara användbart att förstå och felsöka din kunskapsuppsättning.

Upprepa föregående övning, inklusive ett `enriched`-fält för att fånga innehållet i ett berikat dokument:

### <a name="request-body-syntax"></a>Begärandetextsyntax
```json
{
  "fields": [
    {
      "name": "id",
      "type": "Edm.String",
      "key": true,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "sortable": true
    },
    {
      "name": "content",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "languageCode",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "keyPhrases",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "organizations",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "sortable": false,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```
<a name="reset"></a>

## <a name="reset-and-rerun"></a>Återställa och köra igen

I de tidiga experimentella stadierna för pipelineutvecklingen är det mest praktiskt för designiterationer att ta bort objekten från Azure Search och låta koden återskapa dem. Resursnamn är unika. Om du tar bort ett objekt kan du återskapa det med samma namn.

Så här indexerar du dokument med de nya definitionerna:

1. Ta bort indexet för att ta bort sparade data. Ta bort indexeraren för att återskapa den på din tjänst.
2. Ändra en kunskapsuppsättning och indexdefinition.
3. Återskapa ett index och en indexerare på tjänsten för att köra pipelinen. 

Du kan använda portalen för att ta bort index, indexerare och färdigheter.

```http
DELETE https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2017-11-11-Preview
api-key: [api-key]
Content-Type: application/json
```

Statuskod 204 returneras vid borttagning.

När koden utvecklas kanske du vill begränsa en strategi för återskapning. Läs mer i informationen om hur du [återskapar ett index](search-howto-reindex.md).

## <a name="takeaways"></a>Lärdomar

Den här självstudien visar de grundläggande stegen för att skapa en utökad indexeringspipeline genom att skapa komponentdelar: en datakälla, kunskapsuppsättning, index och indexerare.

[Fördefinierade kunskaper](cognitive-search-predefined-skills.md) introducerades, tillsammans med en definition av kunskapsuppsättningen och mekanismerna för att sammanlänka kunskaper via in- och utdata. Du har också lärt dig att `outputFieldMappings` i indexerardefinitionen krävs för routningsberikade värden från pipelinen i ett sökbart index på en Azure Search-tjänst.

Slutligen lärde du dig att testa resultat och återställa systemet för ytterligare iterationer. Du har lärt dig att när du utfärdar frågor mot indexet returneras utdata som skapades av pipelinen för berikande indexering. I den här versionen finns det en mekanism för att visa interna konstruktioner (berikade dokument som skapats av systemet). Du har också lärt dig att kontrollera indexerarstatus, och vilka objekt du ska ta bort innan du kör en pipeline igen.

## <a name="clean-up-resources"></a>Rensa resurser

Det snabbaste sättet att rensa upp efter en självstudie är att ta bort resursgruppen som innehåller Azure Search-tjänsten och Azure Blob Service. Förutsatt att du placerade båda tjänsterna i samma grupp, tar du bort resursgruppen för att permanent ta bort allt i den, inklusive tjänsterna och eventuellt lagrat innehåll som du skapade i den här självstudien. På portalen visas resursgruppens namn på översiktssidan för varje tjänst.

## <a name="next-steps"></a>Nästa steg

Anpassa eller utöka pipelinen med anpassade kunskaper. När du skapar en anpassad kunskap och lägger till den i en kunskapsuppsättning kan du publicera text eller bildanalys som du skriver själv. 

> [!div class="nextstepaction"]
> [Exempel: skapa en anpassad färdighet](cognitive-search-create-custom-skill-example.md)

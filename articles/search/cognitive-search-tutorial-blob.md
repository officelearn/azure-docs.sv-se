---
title: 'Självstudiekurs: REST och AI över Azure-blobbar'
titleSuffix: Azure Cognitive Search
description: Gå igenom ett exempel på textextrahering och bearbetning av naturligt språk över innehåll i Blob-lagring med Postman och Azure Cognitive Search REST API:er.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: 8acafa14afab507b704806056efac0f877a47684
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78190730"
---
# <a name="tutorial-use-rest-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Självstudiekurs: Använd REST och AI för att generera sökbart innehåll från Azure-blobbar

Om du har ostrukturerad text eller avbildningar i Azure Blob-lagring kan en [AI-anrikningspipeline](cognitive-search-concept-intro.md) extrahera information och skapa nytt innehåll som är användbart för fulltextsökning eller kunskapsutvinningsscenarier. Även om en pipeline kan bearbeta bilder fokuserar den här REST-självstudien på text, tillämpa språkidentifiering och bearbetning av naturligt språk för att skapa nya fält som du kan använda i frågor, aspekter och filter.

Den här självstudien använder Postman och [REST-API:erna för sökning](https://docs.microsoft.com/rest/api/searchservice/) för att utföra följande uppgifter:

> [!div class="checklist"]
> * Börja med hela dokument (ostrukturerad text) som PDF, HTML, DOCX och PPTX i Azure Blob-lagring.
> * Definiera en pipeline som extraherar text, identifierar språk, känner igen entiteter och identifierar nyckelfraser.
> * Definiera ett index för att lagra utdata (råinnehåll, plus pipelinegenererade namnvärdespar).
> * Kör pipelinen för att starta omvandlingar och analyser och för att skapa och läsa in indexet.
> * Utforska resultat med hjälp av fulltextsökning och en omfattande frågesyntax.

Om du inte har en Azure-prenumeration öppnar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

+ [Azure-lagring](https://azure.microsoft.com/services/storage/)
+ [Skrivbordsappen Postman](https://www.getpostman.com/)
+ [Skapa](search-create-service-portal.md) eller [hitta en befintlig söktjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Du kan använda den kostnadsfria tjänsten för den här självstudien. En kostnadsfri söktjänst begränsar dig till tre index, tre indexerare och tre datakällor. I den här kursen skapar du en av varje. Innan du börjar, se till att du har utrymme på din tjänst för att acceptera de nya resurserna.

## <a name="download-files"></a>Hämta filer

1. Öppna den här [OneDrive-mappen](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) och klicka på **Hämta** längst upp till vänster för att kopiera filerna till datorn. 

1. Högerklicka på zip-filen och välj **Extrahera alla**. Det finns 14 filer av olika slag. Du kommer att använda 7 för denna övning.

## <a name="1---create-services"></a>1 - Skapa tjänster

Den här självstudien använder Azure Cognitive Search för indexering och frågor, Cognitive Services på backend för AI-anrikning och Azure Blob-lagring för att tillhandahålla data. Den här självstudien förblir under den kostnadsfria allokeringen av 20 transaktioner per indexerare per dag på Cognitive Services, så de enda tjänster du behöver skapa är sökning och lagring.

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

1. Klicka på **+ Behållare** om du vill skapa en behållare och namnge den *kugg-sök-demo*.

1. Välj *cog-search-demo* och klicka sedan på **Ladda upp** för att öppna mappen där du sparade nedladdningsfilerna. Markera alla filer som inte är bildfiler. Du borde ha 7 filer. Klicka på **OK** för att ladda upp.

   ![Ladda upp exempelfiler](media/cognitive-search-tutorial-blob/sample-files.png "Ladda upp exempelfiler")

1. Innan du lämnar Azure Storage får du en anslutningssträng så att du kan formulera en anslutning i Azure Cognitive Search. 

   1. Bläddra tillbaka till översiktssidan för ditt lagringskonto (vi använde *blobstragewestus* som exempel). 
   
   1. I det vänstra navigeringsfönstret väljer du **Access-nycklar** och kopierar en av anslutningssträngarna. 

   Anslutningssträngen är en URL som liknar följande exempel:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Spara anslutningssträngen i Anteckningar. Du behöver det senare när du konfigurerar datakällanslutningen.

### <a name="cognitive-services"></a>Cognitive Services

AI-anrikning backas upp av Cognitive Services, inklusive textanalys och datorseende för naturligt språk och bildbehandling. Om ditt mål var att slutföra en verklig prototyp eller projekt, skulle du vid denna punkt tillhandahålla Cognitive Services (i samma region som Azure Cognitive Search) så att du kan koppla den till indexeringsåtgärder.

För den här övningen kan du dock hoppa över resursetablering eftersom Azure Cognitive Search kan ansluta till Cognitive Services bakom kulisserna och ge dig 20 kostnadsfria transaktioner per indexeringskörning. Eftersom den här självstudien använder 7 transaktioner är den kostnadsfria allokeringen tillräcklig. För större projekt bör du planera att etablera Cognitive Services på S0-nivån för betalning per steg. Mer information finns i [Bifoga Kognitiva tjänster](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Den tredje komponenten är Azure Cognitive Search, som du kan [skapa i portalen](search-create-service-portal.md). Du kan använda den kostnadsfria nivån för att slutföra den här genomgången. 

Precis som med Azure Blob-lagring tar du en stund att samla in åtkomstnyckeln. Vidare, när du börjar strukturera begäranden, måste du ange slutpunkten och admin api-nyckeln som används för att autentisera varje begäran.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Skaffa en api-nyckel och URL för Azure Cognitive Search

1. [Logga in på Azure-portalen](https://portal.azure.com/)och på **sidan** Översikt för söktjänsten får du namnet på söktjänsten. Du kan bekräfta ditt tjänstnamn genom att granska slutpunktsadressen. Om slutpunktsadressen var `https://mydemo.search.windows.net`skulle `mydemo`tjänstnamnet vara .

2. I **Inställningsnycklar** > **Keys**får du en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara admin nycklar, som tillhandahålls för kontinuitet i verksamheten om du behöver rulla en över. Du kan använda antingen primär- eller sekundärnyckeln på begäranden om att lägga till, ändra och ta bort objekt.

   Hämta frågenyckeln också. Det är en bra idé att utfärda frågebegäranden med skrivskyddad åtkomst.

   ![Hämta tjänstens namn och administratörs- och frågenycklar](media/search-get-started-nodejs/service-name-and-keys.png)

Alla begäranden kräver en api-nyckel i huvudet på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, per begäran, mellan programmet som skickar begäran och den tjänst som hanterar den.

## <a name="2---set-up-postman"></a>2 - Ställ in Brevbärare

Starta Postman och konfigurera en HTTP-begäran. Om du inte känner till det här verktyget läser du [Utforska Azure Cognitive Search REST API:er med Postman](search-get-started-postman.md).

De metoder för begäran som används i den här självstudien är **POST**, **PUT**och **GET**. Du ska använda metoderna för att ringa fyra API-anrop till söktjänsten: skapa en datakälla, en kompetens, ett index och en indexerare.

I Rubriker anger du "Innehållstyp" till `application/json` och anger `api-key` till administratörs-api-nyckeln för din Azure Cognitive Search-tjänst. När du har angett rubrikerna kan du använda dem för varje begäran i den här övningen.

  ![URL och rubrik för postmanbegäran](media/search-get-started-postman/postman-url.png "URL och rubrik för postmanbegäran")

## <a name="3---create-the-pipeline"></a>3 - Skapa pipelinen

I Azure Cognitive Search sker AI-bearbetning under indexering (eller datainmatning). Den här delen av genomgången skapar fyra objekt: datakälla, indexdefinition, kompetens, indexerare. 

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla

Ett [datakällobjekt](https://docs.microsoft.com/rest/api/searchservice/create-data-source) tillhandahåller anslutningssträngen till Blob-behållaren som innehåller filerna.

1. Använd **POST** och följande webbadress och ersätt ditt-servicenamn med tjänstens faktiska namn.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2019-05-06
   ```

1. I begäran **brödtext,** kopiera följande JSON definition, ersätta `connectionString` med den faktiska anslutningen av ditt lagringskonto. 

   Kom ihåg att redigera behållarnamnet också. Vi föreslog "cog-search-demo" för behållarnamnet i ett tidigare steg.

    ```json
    {
      "name" : "cog-search-demo-ds",
      "description" : "Demo files to demonstrate cognitive search capabilities.",
      "type" : "azureblob",
      "credentials" :
      { "connectionString" :
        "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-ACCOUNT-KEY>;"
      },
      "container" : { "name" : "<YOUR-BLOB-CONTAINER-NAME>" }
    }
    ```
1. Skicka begäran. Du bör se en statuskod för 201 som bekräftar framgång. 

Om du ser felet 403 eller 404 ska du kontrollera konstruktionen för begäran: `api-version=2019-05-06` ska vara på slutpunkten, `api-key` ska vara i rubriken efter `Content-Type` och dess värde måste vara giltigt för en söktjänst. Du kanske vill köra JSON-dokumentet via en online-JSON-validerare för att kontrollera att syntaxen är korrekt. 

### <a name="step-2-create-a-skillset"></a>Steg 2: Skapa en kompetens

Ett [skillset-objekt](https://docs.microsoft.com/rest/api/searchservice/create-skillset) är en uppsättning anrikningssteg som tillämpas på innehållet. 

1. Använd **PUT** och följande webbadress och ersätter DITT-SERVICE-NAMN med tjänstens faktiska namn.

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-ss?api-version=2019-05-06
    ```

1. Kopiera JSON-definitionen nedan i **begäran.** Denna kompetens består av följande inbyggda färdigheter.

   | Skicklighet                 | Beskrivning    |
   |-----------------------|----------------|
   | [Igenkänning av enhet](cognitive-search-skill-entity-recognition.md) | Extraherar namnen på personer, organisationer och platser från innehåll i blob-behållaren. |
   | [Språkidentifiering](cognitive-search-skill-language-detection.md) | Identifierar innehållets språk. |
   | [Textuppdelning](cognitive-search-skill-textsplit.md)  | Bryter stor innehåll i mindre bitar innan du anropar nyckelfrasen extrahering skicklighet. Extrahering av nyckelfraser accepterar indata på 50 000 tecken eller mindre. Några av exempelfilerna måste delas upp för att rymmas inom gränsen. |
   | [Extrahering av diskussionsämne](cognitive-search-skill-keyphrases.md) | Drar ut de översta tangentfraserna. |

   Varje kunskap körs på innehållet i dokumentet. Under bearbetningen knäcker Azure Cognitive Search varje dokument för att läsa innehåll från olika filformat. Text som hittas från källfilen placeras i ett genererat ```content```-fält, ett för varje dokument. Som sådan blir ```"/document/content"```ingången .

   För extrahering av nyckelfraser, eftersom vi använder textdelningsfärdigheten för att ```"document/pages/*"``` dela upp större filer ```"/document/content"```på sidor, är kontexten för nyckelfrasen extraktionsfärdighet (för varje sida i dokumentet) i stället för .

    ```json
    {
      "description": "Extract entities, detect language and extract key-phrases",
      "skills":
      [
        {
          "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
          "categories": [ "Person", "Organization", "Location" ],
          "defaultLanguageCode": "en",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "persons", "targetName": "persons" },
            { "name": "organizations", "targetName": "organizations" },
            { "name": "locations", "targetName": "locations" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "languageCode", "targetName": "languageCode" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
          "textSplitMode" : "pages",
          "maximumPageLength": 4000,
          "inputs": [
            { "name": "text", "source": "/document/content" },
            { "name": "languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "textItems", "targetName": "pages" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
          "context": "/document/pages/*",
          "inputs": [
            { "name": "text", "source": "/document/pages/*" },
            { "name":"languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "keyPhrases", "targetName": "keyPhrases" }
          ]
        }
      ]
    }
    ```
    En grafisk representation av kunskapsuppsättningen visas nedan. 

    ![Förstå en kompetens](media/cognitive-search-tutorial-blob/skillset.png "Förstå en kompetens")

1. Skicka begäran. Brevbäraren bör returnera en statuskod för 201 som bekräftar framgång. 

> [!NOTE]
> Utdata kan mappas till ett index som används som indata till en underordnad kunskap, eller både, vilket är fallet med språkkod. I indexet kan en språkkod användas för filtrering. Som indata används språkkoden av textanalyskunskaper för att informera om de språkliga reglerna kring ordnedbrytning. Mer information om grunderna i kunskapsuppsättningar finns i [Definiera en kunskapsuppsättning](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>Steg 3: Skapa ett index

Ett [index](https://docs.microsoft.com/rest/api/searchservice/create-index) innehåller det schema som används för att skapa det fysiska uttrycket för ditt innehåll i inverterade index och andra konstruktioner i Azure Cognitive Search. Den största komponenten i ett index är fältsamlingen, där datatyp och attribut avgör innehåll och beteenden i Azure Cognitive Search.

1. Använd **PUT** och följande webbadress, som ersätter DITT-SERVICE-NAMN med tjänstens faktiska namn, för att namnge ditt index.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2019-05-06
   ```

1. Kopiera följande JSON-definition i **begäran.** Fältet `content` lagrar själva dokumentet. Ytterligare fält `languageCode` `keyPhrases`för `organizations` , och representerar ny information (fält och värden) som skapats av kompetensen.

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
          "name": "metadata_storage_name",
          "type": "Edm.String",
          "searchable": false,
          "filterable": false,
          "facetable": false,
          "sortable": false
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
          "name": "persons",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "organizations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "locations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        }
      ]
    }
    ```

1. Skicka begäran. Brevbäraren bör returnera en statuskod för 201 som bekräftar framgång. 

### <a name="step-4-create-and-run-an-indexer"></a>Steg 4: Skapa och kör en indexerare

En [Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer) driver pipelinen. De tre komponenter som du har skapat hittills (datakälla, kompetens, index) är indata till en indexerare. Att skapa indexeraren på Azure Cognitive Search är den händelse som försätter hela pipelinen i rörelse. 

1. Använd **PUT** och följande webbadress, som ersätter DITT-SERVICE-NAMN med tjänstens faktiska namn, för att namnge din indexerare.

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
   ```

1. Kopiera JSON-definitionen nedan i **begäran.** Lägg märke till fältmappningselementen. Dessa mappningar är viktiga eftersom de definierar dataflödet. 

   Bearbetas `fieldMappings` före kompetensen och skickar innehåll från datakällan till målfälten i ett index. Du använder fältmappningar för att skicka befintligt, oföränderat innehåll till indexet. Om fältnamn och typer är desamma i båda ändar krävs ingen mappning.

   De `outputFieldMappings` är för områden som skapats av färdigheter, och därmed bearbetas efter kompetensen har körts. Referenserna till `sourceFieldNames` `outputFieldMappings` in finns inte förrän dokumentsprickor eller anrikning skapar dem. Fältet `targetFieldName` är ett fält i ett index som definieras i indexschemat.

    ```json
    {
      "name":"cog-search-demo-idxr",    
      "dataSourceName" : "cog-search-demo-ds",
      "targetIndexName" : "cog-search-demo-idx",
      "skillsetName" : "cog-search-demo-ss",
      "fieldMappings" : [
        {
          "sourceFieldName" : "metadata_storage_path",
          "targetFieldName" : "id",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "metadata_storage_name",
          "targetFieldName" : "metadata_storage_name",
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
          "sourceFieldName" : "/document/persons",
          "targetFieldName" : "persons"
        },
        {
          "sourceFieldName" : "/document/organizations",
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/locations",
          "targetFieldName" : "locations"
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
          "parsingMode": "default",
          "firstLineContainsHeaders": false,
          "delimitedTextDelimiter": ","
        }
      }
    }
    ```

1. Skicka begäran. Brevbäraren ska returnera en statuskod för 201 som bekräftar lyckad bearbetning. 

   Förvänta dig att steget kan ta flera minuter att slutföra. Trots att datauppsättningen är liten är analytiska kunskaper beräkningsintensiva. 

> [!NOTE]
> När en indexerare skapas anropas pipelinen. Om det uppstår problem med att ansluta till data, mappningsindata eller -utdata eller ordningen på åtgärder visas dem i det här stadiet. Du kan behöva ta bort objekt först om du vill köra pipelinen med kod- eller skriptändringar. Mer information finns i [Reset and re-run](#reset) (Återställa och köra om).

#### <a name="about-indexer-parameters"></a>Om indexerarparametrar

Skriptet ställer in ```"maxFailedItems"```  på -1, vilket instruerar indexeringsmotorn att ignorera fel under dataimport. Detta är acceptabelt eftersom det finns så få dokument i demonstrationsdatakällan. För en större datakälla skulle du ställa in värdet på större än 0.

Uttalandet ```"dataToExtract":"contentAndMetadata"``` talar om för indexeraren att automatiskt extrahera innehållet från olika filformat samt metadata relaterade till varje fil. 

När innehållet har extraherats kan du ställa in ```imageAction``` på att extrahera text från avbildningar som hittades i datakällan. Konfigurationen av ```"imageAction":"generateNormalizedImages"```, tillsammans med OCR-färdigheten och färdigheten för textsammanslagning, talar om för indexeraren att den ska extrahera text från bilderna (exempelvis ordet ”stopp” från en trafikstoppskylt) och bädda in den som en del av innehållsfältet. Det här beteendet gäller både avbildningarna som är inbäddade i dokumenten (tänk på en avbildning i en PDF) samt avbildningar som hittas i datakällan, till exempel en JPG-fil.

## <a name="4---monitor-indexing"></a>4 - Bildskärmsindexering

Indexering och anrikning påbörjas så snart du skickar in begäran skapa indexerare. Beroende på vilka kognitiva färdigheter du definierade kan indexering ta ett tag. Skicka följande begäran för att kontrollera indexerarens status om du vill ta reda på om indexeraren fortfarande körs.

1. Använd **GET** och följande webbadress, som ersätter DITT-SERVICE-NAMN med tjänstens faktiska namn, för att namnge din indexerare.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2019-05-06
   ```

1. Granska svaret för att ta reda på om indexeraren körs eller för att visa fel- och varningsinformation.  

Om du använder den kostnadsfria nivån förväntas följande meddelande: '"Det gick inte att extrahera innehåll eller metadata från dokumentet. Trunkerad extraherad text till "32768" tecken". Det här meddelandet visas eftersom blob-indexering på den kostnadsfria nivån har en[32K-gräns för teckenutvinning](search-limits-quotas-capacity.md#indexer-limits). Det här meddelandet visas inte för den här datauppsättningen på högre nivåer. 

> [!NOTE]
> Varningar är vanliga i vissa scenarier och tyder inte alltid på ett problem. Om till exempel en blob-behållare innehåller bildfiler och pipelinen inte hanterar bilder får du en varning om att bilderna inte har bearbetats.

## <a name="5---search"></a>5 - Sök

Nu när du har skapat nya fält och information ska vi köra några frågor för att förstå värdet av kognitiv sökning när det relaterar till ett typiskt sökscenario.

Kom ihåg att vi började med blob-innehåll, där `content` hela dokumentet är paketerat i ett enda fält. Du kan söka i det här fältet och hitta matchningar till dina frågor.

1. Använd **GET** och följande WEBBADRESS, som ersätter YOUR-SERVICE-NAME med tjänstens faktiska namn, för att `content` söka efter förekomster av en term eller fras, returnera fältet och antalet matchande dokument.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?search=*&$count=true&$select=content?api-version=2019-05-06
   ```
   
   Resultatet av den här frågan returnerar dokumentinnehåll, vilket är samma resultat som du skulle få om du används blob indexeraren utan kognitiv sökning pipeline. Det här fältet är sökbart, men ogenomförbart om du vill använda fasetter, filter eller komplettera automatiskt.

   ![Utdata för innehållsfält](media/cognitive-search-tutorial-blob/content-output.png "Utdata för innehållsfält")
   
1. För den andra frågan returnerar du några av de nya fält som skapats av pipelinen (personer, organisationer, platser, languageCode). Vi utelämnar nyckelfraser för korthet, men du bör inkludera det om du vill se dessa värden.

   ```http
   https://mydemo.search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2019-05-06
   ```
   Fälten i $select-satsen innehåller ny information som skapats från cognitive services funktioner för bearbetning av naturligt språk. Som du kanske tror finns det en del buller i resultat och variation mellan dokument, men i många fall ger de analytiska modellerna korrekta resultat.

   Följande bild visar resultat för Satya Nadellas öppna brev när han tillträder vd-rollen på Microsoft.

   ![Utdata för rörledningar](media/cognitive-search-tutorial-blob/pipeline-output.png "Utdata för rörledningar")

1. Om du vill se hur du kan dra nytta av dessa fält lägger du till en aspektparameter för att returnera en aggregering av matchande dokument efter plats.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2019-05-06
   ``` 

   I det här exemplet finns det 2 eller 3 matcher för varje plats.

   ![Aspektutgång](media/cognitive-search-tutorial-blob/facet-output.png "Aspektutgång")
   

1. I det här sista exemplet använder du ett filter på organisationssamlingen och returnerar två matchningar för filtervillkor baserat på NASDAQ.

   ```http
   cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2019-05-06
   ```

Dessa frågor illustrerar några av de sätt du kan arbeta med frågesyntax och filter på nya fält som skapats av kognitiv sökning. Fler frågeexempel finns i Exempel i REST API för [sökdokument,](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) [enkla syntaxfrågeexempel](search-query-simple-examples.md)och [fullständiga lucene-frågeexempel](search-query-lucene-examples.md).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Återställa och köra igen

I de tidiga experimentella utvecklingsstadierna är den mest praktiska metoden för designiteration att ta bort objekten från Azure Cognitive Search och låta koden återskapa dem. Resursnamn är unika. Om du tar bort ett objekt kan du återskapa det med samma namn.

Du kan använda portalen för att ta bort index, indexerare, datakällor och skillsets. När du tar bort indexeraren kan du också selektivt ta bort index, kompetens och datakälla samtidigt.

![Ta bort sökobjekt](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Ta bort sökobjekt i portalen")

Eller använd **DELETE** och ange webbadresser till varje objekt. Följande kommando tar bort en indexerare.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
```

Statuskod 204 returneras vid borttagning.

## <a name="takeaways"></a>Lärdomar

Den här självstudien visar de grundläggande stegen för att skapa en utökad indexeringspipeline genom att skapa komponentdelar: en datakälla, kunskapsuppsättning, index och indexerare.

[Inbyggda färdigheter](cognitive-search-predefined-skills.md) infördes, tillsammans med skillset definition och mekanik kedja färdigheter tillsammans genom ingångar och utgångar. Du lärde dig `outputFieldMappings` också att i indexeringsdefinitionen krävs för routning av utökade värden från pipelinen till ett sökbart index på en Azure Cognitive Search-tjänst.

Slutligen lärde du dig att testa resultat och återställa systemet för ytterligare iterationer. Du har lärt dig att när du utfärdar frågor mot indexet returneras utdata som skapades av pipelinen för berikande indexering. 

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration är det i slutet av ett projekt en bra idé att ta bort de resurser som du inte längre behöver. Resurser som fortsätter att köras kan medföra kostnader. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken Alla resurser eller Resursgrupper i fönstret för vänsternavigering.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med alla objekt i en AI-anrikningspipeline, låt oss ta en närmare titt på kompetensdefinitioner och individuella färdigheter.

> [!div class="nextstepaction"]
> [Hur man skapar en kompetens](cognitive-search-defining-skillset.md)
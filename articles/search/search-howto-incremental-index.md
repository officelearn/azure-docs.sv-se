---
title: Konfigurera cache och inkrementell anrikning (förhandsgranskning)
titleSuffix: Azure Cognitive Search
description: Aktivera cachelagring och bevara tillståndet för berikat innehåll för kontrollerad bearbetning i en kognitiv skillset. Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 66bac2a063a3257a2101ca2f30e5946264adb9ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989560"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>Konfigurera cachelagring för inkrementell anrikning i Azure Cognitive Search

> [!IMPORTANT] 
> Inkrementell berikning är för närvarande i offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API-versionen 2019-05-06-Preview](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för portalen eller .NET SDK.

Den här artikeln visar hur du lägger till cachelagring i en anrikningspipeline så att du stegvis kan ändra steg utan att behöva återskapa varje gång. Som standard är en kompetens tillståndslös, och om du ändrar någon del av dess sammansättning krävs en fullständig repris av indexeraren. Med inkrementell berikning kan indexeraren avgöra vilka delar av dokumentträdet som behöver uppdateras baserat på ändringar som identifierats i kompetensuppsättningen eller indexeraren. Befintligt bearbetat resultat bevaras och återanvänds där så är möjligt. 

Cachelagrat innehåll placeras i Azure Storage med hjälp av kontoinformation som du anger. Behållaren, `ms-az-search-indexercache-<alpha-numerc-string>`med namnet, skapas när du kör indexeraren. Det bör betraktas som en intern komponent som hanteras av söktjänsten och får inte ändras.

Om du inte är bekant med att konfigurera indexerare börjar du med [indexeraröversikt](search-indexer-overview.md) och fortsätter sedan till [skillsets](cognitive-search-working-with-skillsets.md) för att lära dig mer om anrikningspipelor. Mer bakgrund om nyckelbegrepp finns i [inkrementell berikning](cognitive-search-incremental-indexing-conceptual.md).

## <a name="enable-caching-on-an-existing-indexer"></a>Aktivera cachelagring på en befintlig indexerare

Om du har en befintlig indexerare som redan har en kompetens följer du stegen i det här avsnittet för att lägga till cachelagring. Som en engångsåtgärd måste du återställa och köra indexeraren igen i sin helhet innan inkrementell bearbetning kan börja gälla.

> [!TIP]
> Som proof-of-concept kan du köra igenom den här [portalen snabbstart](cognitive-search-quickstart-blob.md) för att skapa nödvändiga objekt och sedan använda Postman eller portalen för att göra dina uppdateringar. Du kanske vill bifoga en fakturerbar Cognitive Services-resurs. Köra indexeraren flera gånger kommer att uttömma den kostnadsfria dagliga allokeringen innan du kan slutföra alla steg.

### <a name="step-1-get-the-indexer-definition"></a>Steg 1: Hämta indexeringsdefinitionen

Börja med en giltig, befintlig indexerare som har dessa komponenter: datakälla, kompetens, index. Din indexerare bör vara körbar. 

Med hjälp av en API-klient konstruerar du en [GET Indexer-begäran för](https://docs.microsoft.com/rest/api/searchservice/get-indexer) att hämta den aktuella konfigurationen av indexeraren. När du använder api-versionen för förhandsversionen `cache` till GET-indexeraren läggs en egenskap inställd på null till i definitionerna.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Kopiera indexeringsdefinitionen från svaret.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>Steg 2: Ändra cacheegenskapen i indexeringsdefinitionen

Som standard `cache` är egenskapen null. Använd en API-klient för att ställa in cachekonfigurationen (portalen stöder inte den här partikeluppdateringen). 

Ändra cacheobjektet så att det innehåller följande obligatoriska och valfria egenskaper: 

+ Den `storageConnectionString` krävs och måste ställas in på en Azure-lagringsanslutningssträng. 
+ Egenskapen `enableReprocessing` boolean är`true` valfri (som standard), och den anger att inkrementell anrikning är aktiverad. När det behövs kan `false` du ställa in den på att avbryta inkrementell bearbetning medan andra resursintensiva `true` åtgärder, till exempel indexering av nya dokument, pågår och sedan vrida tillbaka den till senare.

```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

### <a name="step-3-reset-the-indexer"></a>Steg 3: Återställ indexeraren

En återställning av indexeraren krävs när du ställer in inkrementell anrikning för befintliga indexerare för att säkerställa att alla dokument är i ett konsekvent tillstånd. Du kan använda portalen eller en API-klient och [REST-API:et för återställningsindexerare](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) för den här uppgiften.

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>Steg 4: Spara den uppdaterade definitionen

[Uppdatera indexeraren](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) med en PUT-begäran, brödtexten i begäran ska innehålla den uppdaterade indexeringsdefinitionen som har cacheegenskapen. Om du får en 400, kontrollera indexeringsdefinitionen för att se till att alla krav är uppfyllda (datakälla, kompetens, index).

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
{
    "name" : "<YOUR-INDEXER-NAME>",
    ...
    "cache": {
        "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    }
}
```

Om du nu utfärdar en annan GET-begäran på indexeraren inkluderas en `ID` egenskap i cacheobjektet. Den alfanumeriska strängen läggs till namnet på behållaren som innehåller alla cachelagrade resultat och mellanliggande tillstånd för varje dokument som bearbetas av den här indexeraren. ID:n används för att unikt namnge cacheminnet i Blob-lagring.

    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }

### <a name="step-5-run-the-indexer"></a>Steg 5: Kör indexeraren

Om du vill köra indexeraren kan du använda portalen eller API:et. Välj indexeraren i listan indexerare i portalen och klicka på **Kör**. En fördel med att använda portalen är att du kan övervaka indexerarstatus, notera jobbets varaktighet och hur många dokument som bearbetas. Portalsidor uppdateras med några minuters mellanrum.

Du kan också använda REST för att [köra indexeraren:](https://docs.microsoft.com/rest/api/searchservice/run-indexer)

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

När indexeraren har körts hittar du cacheminnet i Azure Blob-lagring. Behållarnamnet är i följande format:`ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> En återställning och omkörning av indexeraren resulterar i en fullständig ombyggnad så att innehållet kan cachelagras. Alla kognitiva berikanden kommer att köras på alla dokument.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>Steg 6: Ändra en kompetens och bekräfta inkrementell berikning

Om du vill ändra en kompetens kan du använda portalen eller API:et. Om du till exempel använder textöversättning räcker `en` det `es` med en enkel infogad ändring från till eller ett annat språk för proof-of-concept-testning av inkrementell berikning.

Kör indexeraren igen. Endast de delar av ett berikat dokumentträd uppdateras. Om du använde [portalens snabbstart](cognitive-search-quickstart-blob.md) som proof-of-concept, ändra texten översättning skicklighet till "es", kommer du att märka att endast 8 dokument uppdateras i stället för den ursprungliga 14. Bildfiler som inte påverkas av översättningsprocessen återanvänds från cacheminnet.

## <a name="enable-caching-on-new-indexers"></a>Aktivera cachelagring på nya indexerare

Om du vill ställa in inkrementell berikning för `cache` en ny indexerare behöver du bara inkludera egenskapen i indexeringsdefinitionsnyttolasten när [du anropar Skapa indexerare (2019-05-06-Preview).](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 


```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
    }
}
```

## <a name="checking-for-cached-output"></a>Söka efter cachelagrade utdata

Cachen skapas, används och hanteras av indexeraren och dess innehåll representeras inte i ett format som är läsbart för människor. Det bästa sättet att avgöra om cacheminnet används är genom att köra indexeraren och jämföra före och efter-mått för körningstid och antal dokument. 

Anta till exempel en kompetensuppsättning som börjar med bildanalys och OCR (Optical Character Recognition) av skannade dokument, följt av nedströmsanalys av den resulterande texten. Om du ändrar en textfärdighet i nedströms kan indexeraren hämta allt tidigare bearbetat bild- och OCR-innehåll från cacheminnet, uppdatera och bearbeta bara textrelaterade ändringar som indikeras av dina redigeringar. Du kan förvänta dig färre dokument i antalet dokument (till exempel 8/8 i motsats till 14/14 i den ursprungliga körningen), kortare körningstider och färre avgifter på din faktura.

## <a name="working-with-the-cache"></a>Arbeta med cacheminnet

När cacheminnet är i drift kontrollerar indexerare cacheminnet när [Run Indexer](https://docs.microsoft.com/rest/api/searchservice/run-indexer) anropas för att se vilka delar av den befintliga utdata som kan användas. 

I följande tabell sammanfattas hur olika API:er relaterar till cachen:

| API           | Cache-påverkan     |
|---------------|------------------|
| [Skapa indexerare (2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) | Skapar och kör en indexerare vid första användningen, inklusive att skapa en cache om indexeringsdefinitionen anger den. |
| [Kör indexerare](https://docs.microsoft.com/rest/api/searchservice/run-indexer) | Kör en anrikningspipeline på begäran. Det här API:et läser från cacheminnet om det finns, eller skapar en cache om du har lagt till cachelagring i en uppdaterad indexeringsdefinition. När du kör en indexerare som har cachelagring aktiverad utelämnar indexeraren steg om cachelagrade utdata kan användas. Du kan använda den allmänt tillgängliga eller förhandsgranska API-versionen av det här API:et.|
| [Återställ indexeraren](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)| Rensar indexeraren för all inkrementell indexeringsinformation. Nästa indexeringskörning (antingen på begäran eller schema) är fullständig upparbetning från grunden, inklusive att köra alla kunskaper igen och återskapa cacheminnet. Det är funktionellt likvärdigt med att ta bort indexeraren och återskapa den. Du kan använda den allmänt tillgängliga eller förhandsgranska API-versionen av det här API:et.|
| [Återställ färdigheter (2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) | Anger vilka kunskaper som ska köras på nästa indexeringskörning, även om du inte har ändrat några kunskaper. Cachen uppdateras i enlighet med detta. Utdata, till exempel ett kunskapslager eller sökindex, uppdateras med hjälp av återanvändbara data från cachen plus nytt innehåll per den uppdaterade färdigheten. |

Mer information om hur du styr vad som händer med cachen finns i [Cachehantering](cognitive-search-incremental-indexing-conceptual.md#cache-management).

## <a name="next-steps"></a>Nästa steg

Inkrementell berikning är tillämplig på indexerare som innehåller skillsets. Som ett nästa steg, besök kompetensdokumentationen för att förstå begrepp och sammansättning. 

När du har aktiverat cacheminnet vill du dessutom veta vilka parametrar och API:er som påverkar cachelagringen, inklusive hur du åsidosätter eller tvingar vissa beteenden. Mer information finns i följande länkar.

+ [Skillset begrepp och sammansättning](cognitive-search-working-with-skillsets.md)
+ [Hur man skapar en kompetens](cognitive-search-defining-skillset.md)
+ [Introduktion till inkrementell berikning och cachelagring](cognitive-search-incremental-indexing-conceptual.md)

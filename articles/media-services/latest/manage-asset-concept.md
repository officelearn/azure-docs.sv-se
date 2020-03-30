---
title: Hantera resurser i Azure Media Services
titleSuffix: Azure Media Services
description: En tillgång där du matar in media (till exempel via uppladdning eller live-mat, utdatamedia (från en jobbutdata) och publicerar media från (för direktuppspelning). Det här avsnittet ger en översikt över hur du skapar en ny tillgång och laddar upp filer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: fcdb8af770fa0068e8413d4609a56223a9a20ce2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345902"
---
# <a name="manage-assets"></a>Hantera tillgångar

I Azure Media Services är en [tillgång](https://docs.microsoft.com/rest/api/media/assets) där du 

* ladda upp mediefiler till en tillgång,
* intag och arkivera liveströmmar till en tillgång,
* resultaten av en kodning av analysjobb till en tillgång,
* publicera media för direktuppspelning, 
* hämta filer från en tillgång.

Det här avsnittet innehåller en översikt över hur du laddar upp filer till en tillgång och utför några andra vanliga åtgärder. Det ger också länkar till kodexempel och relaterade ämnen.

## <a name="prerequisite"></a>Krav 

Innan du börjar utveckla, granska:

* [Begrepp](concepts-overview.md)
* [Utveckla med Api:er för mediatjänster v3](media-services-apis-overview.md) (innehåller information om åtkomst till API:er, namngivningskonventioner och så vidare) 

## <a name="upload-media-files-into-an-asset"></a>Ladda upp mediefiler till en tillgång

När de digitala filerna har överförts till lagring och associeras med en tillgång kan de användas i Media Services-kodningen, direktuppspelningen och analysen av innehållsarbetsflöden. Ett av de vanliga Media Services-arbetsflödena är att ladda upp, koda och strömma en fil. I det här avsnittet beskrivs de allmänna stegen.

1. Använd Media Services v3 API för att skapa en ny ”indataresurs”. Den här åtgärden skapar en container i det lagringskonto som associeras med ditt Media Services-konto. API:et returnerar behållarnamnet (till exempel `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).

    Om du redan har en blob-behållare som du vill associera med en tillgång kan du ange behållarnamnet när du skapar tillgången. Media Services stöder för närvarande endast blobar i containerroten och inte med sökvägar i filnamnet. Därmed fungerar en container med namnet ”input.mp4”. En behållare med filnamnet "videos/inputs/input.mp4" fungerar dock inte.

    Du kan använda Azure CLI för att ladda upp direkt till valfritt lagringskonto och container som du har rättigheter till i din prenumeration.

    Containerns namn måste vara unikt och följa riktlinjerna för namngivning för lagring. Namnet måste inte följa formateringen för Media Services-tillgångscontainerns namn (tillgångs-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Hämta en SAS-URL med läs- och skrivbehörigheter som används för att ladda upp digitala filer till tillgångscontainern.

    Du kan använda Media Services API för att [lista URL:er för tillgångscontainern](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).

    **AssetContainerSas.listContainerSas** tar en [ListContainerSasInput-parameter](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) som du anger `expiryTime`. Tiden ska vara inställd på < 24 timmar.

    [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) returnerar flera SAS-url:er eftersom det finns två lagringskontonycklar för varje lagringskonto. Ett lagringskonto har två nycklar eftersom det möjliggör sömlös rotation av lagringskontonycklar (till exempel ändra en när du använder den andra och börja använda den nya nyckeln och rotera den andra nyckeln). Den första SAS-URL:en representerar lagringsnyckel1 och andra en lagringsnyckel2.
3. Använd Azure Storage API:er eller SDK:er (till exempel [STORAGE REST API](../../storage/common/storage-rest-api-auth.md) eller [.NET SDK)](../../storage/blobs/storage-quickstart-blobs-dotnet.md)för att överföra filer till tillgångsbehållaren.
4. Använd Media Services v3-API:er för att skapa en transformering och ett jobb för att bearbeta din ”indatatillgång”. Mer information finns i [Transformeringar och jobb](transform-concept.md).
5. Strömma innehållet från "utdata"-tillgången.

### <a name="create-a-new-asset"></a>Skapa en ny tillgång

> [!NOTE]
> En tillgångs egenskaper för datetime-typen är alltid i UTC-format.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Ett REST-exempel finns i exemplet [Skapa en tillgång med REST.](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples)

Exemplet visar hur du skapar den **begärande brödtext** där du kan ange beskrivning, behållarnamn, lagringskonto och annan användbar information.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

### <a name="see-also"></a>Se även

* [Skapa en jobbindata från en lokal fil](job-input-from-local-file-how-to.md)
* [Skapa en jobbinmatning från en HTTPS-URL](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>Inta och arkivera liveströmmar till en tillgång

I Media Services är ett [Live Output-objekt](https://docs.microsoft.com/rest/api/media/liveoutputs) som en digital videobandspelare som fångar och spelar in din livestream till en tillgång i ditt Media Services-konto. Det inspelade innehållet sparas i behållaren som definieras av [resursen Tillgång.](https://docs.microsoft.com/rest/api/media/assets)

Mer information finns i:

* [Använda en molnbaserad DVR-spelare](live-event-cloud-dvr.md)
* [Streaming live handledning](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>Utdata från ett jobb till en tillgång

I Media Services måste du när du bearbetar dina videor (till exempel kodning eller analys) skapa en [utdatatillgång](assets-concept.md) för att lagra resultatet av [ditt jobb](transforms-jobs-concept.md).

Mer information finns i:

* [Koda en video](encoding-concept.md)
* [Skapa en jobbindata från en lokal fil](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>Publicera en tillgång för direktuppspelning

Om du vill publicera en tillgång för direktuppspelning måste du skapa en [streamingpositionerare](streaming-locators-concept.md). Den direktuppspelningspositioneraren måste känna till det tillgångsnamn som du vill publicera. 

Mer information finns i:

[Självstudiekurs: Ladda upp, koda och strömma videor med Media Services v3](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>Hämta resultat för ett jobb från en utdatatillgång

Du kan sedan hämta dessa resultat av jobbet till en lokal mapp med hjälp av API:er för medietjänst och lagring. 

Se exemplet [med nedladdningsfiler.](download-results-howto.md)

## <a name="filtering-ordering-paging"></a>Filtrering, beställning, växling

Se [Filtrering, beställning, växling av Media Services-entiteter](entities-overview.md).

## <a name="next-steps"></a>Nästa steg

Se de fullständiga kodexemplen som visar hur du laddar upp, kodar, analyserar, streamar live och på begäran: 

* [Java](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/), 
* [.NET](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/), 
* [VILA](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).

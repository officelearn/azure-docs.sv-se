---
title: Hantera till gångar i Azure Media Services
titleSuffix: Azure Media Services
description: En till gång där du anger media (t. ex. via överföring eller Live-inmatning), utdata-medium (från ett jobb för utdata) och publicerar media från (för strömning). Det här avsnittet ger en översikt över hur du skapar en ny till gång och laddar upp filer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 1e912e8147f83571d073d5456019d043ff983ceb
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925623"
---
# <a name="manage-assets"></a>Hantera tillgångar

I Azure Media Services är en [till gång](https://docs.microsoft.com/rest/api/media/assets) där du 

* Ladda upp mediefiler till en till gång,
* Mata in och arkivera Live-strömmar till en till gång,
* resultat av en kodning för analys jobb till en till gång
* publicera media för strömning, 
* Ladda ned filer från en till gång.

Det här avsnittet innehåller en översikt över hur du överför filer till en till gång och utför andra vanliga åtgärder. Den innehåller också länkar till kod exempel och närliggande ämnen.

## <a name="prerequisite"></a>Krav 

Innan du börjar utveckla bör du gå igenom följande:

* [Koncept](concepts-overview.md)
* [Utveckla med Media Services v3-API: er](media-services-apis-overview.md) (innehåller information om åtkomst till API: er, namn konventioner och så vidare) 

## <a name="upload-media-files-into-an-asset"></a>Ladda upp mediefiler till en till gång

När de digitala filerna har överförts till lagring och associerats med en till gång kan de användas i Media Services kodning, strömning och analys av innehålls arbets flöden. En av vanliga Media Services arbets flöden är att ladda upp, koda och strömma en fil. Det här avsnittet beskriver de allmänna stegen.

1. Använd API:et för Media Services v3 för att skapa en ny ”indataresurs”. Den här åtgärden skapar en container i det lagringskonto som associeras med ditt Media Services-konto. API: et returnerar behållar namnet (till exempel `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).

    Om du redan har en BLOB-behållare som du vill koppla till en till gång kan du ange behållar namnet när du skapar till gången. Media Services stöder för närvarande endast blobar i containerroten och inte med sökvägar i filnamnet. Därmed fungerar en container med namnet ”input.mp4”. Men en behållare med fil namnet "videor/Inputs/input. mp4" fungerar inte.

    Du kan använda Azure CLI för att ladda upp direkt till valfritt lagringskonto och container som du har rättigheter till i din prenumeration.

    Containerns namn måste vara unikt och följa riktlinjerna för namngivning för lagring. Namnet måste inte följa formateringen för Media Services-tillgångscontainerns namn (tillgångs-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Hämta en SAS-URL med läs- och skrivbehörigheter som används för att ladda upp digitala filer till tillgångscontainern. Du kan använda Media Services-API:et för att [lista URL:er för tillgångscontainern](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Använd Azure Storage-API: er eller SDK: er (till exempel [lagrings REST API](../../storage/common/storage-rest-api-auth.md) eller [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) för att ladda upp filer till till gångs behållaren.
4. Använd Media Services v3-API:er för att skapa en transformering och ett jobb för att bearbeta din ”indatatillgång”. Mer information finns i [Transformeringar och jobb](transform-concept.md).
5. Strömma innehållet från "output"-till gången.

### <a name="create-a-new-asset"></a>Skapa en ny till gång

> [!NOTE]
> En till gångs egenskaper för datetime-typen är alltid i UTC-format.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Ett REST-exempel finns i exemplet [skapa en till gång med rest](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) .

Exemplet visar hur du skapar **begär ande texten** där du kan ange beskrivning, container namn, lagrings konto och annan användbar information.

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

### <a name="also-see"></a>Se även

* [Skapa ett jobb indata från en lokal fil](job-input-from-local-file-how-to.md)
* [Skapa ett jobb inmatat från en HTTPS-URL](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>Mata in och arkivera Live-strömmar till en till gång

I Media Services är ett [Live-utmatnings](https://docs.microsoft.com/rest/api/media/liveoutputs) objekt som en digital videobandspelare som fångar upp och registrerar din Live-dataström till en till gång i ditt Media Services-konto. Det inspelade innehållet sparas i den behållare som definieras av [resurs resursen.](https://docs.microsoft.com/rest/api/media/assets)

Mer information finns i:

* [Använda en molnbaserad DVR-spelare](live-event-cloud-dvr.md)
* [Direkt uppspelnings kurs](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>Spara resultatet av ett jobb till en till gång

I Media Services, när du bearbetar dina videor (till exempel kodning eller analys), måste du skapa en utgående [till gång](assets-concept.md) för att lagra resultatet av [jobbet](transforms-jobs-concept.md).

Mer information finns i:

* [Koda en video](encoding-concept.md)
* [Skapa ett jobb indata från en lokal fil](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>Publicera en till gång för strömning

Om du vill publicera en till gång för strömning måste du skapa en [strömmande lokaliserare](streaming-locators-concept.md). Den strömmande lokaliseraren måste känna till till gångs namnet som du vill publicera. 

Mer information finns i:

[Självstudie: Ladda upp, koda och strömma videor med Media Services v3](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>Hämta resultatet av ett jobb från en utmatnings till gång

Du kan sedan hämta resultatet av ditt jobb till en lokal mapp med hjälp av media service och Storage-API: er. 

Se exempel på [nedladdnings fil](download-results-howto.md) .

## <a name="filtering-ordering-paging"></a>Filtrering, skrivordning, växling

Se [filtrering, sortering, sid indelning för Media Services entiteter](entities-overview.md).

## <a name="next-steps"></a>Nästa steg

Se fullständiga kod exempel som visar hur du överför, kodar, analyserar, strömmar Live och på begäran: 

* [Java](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/), 
* [.Net](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/), 
* [Rest](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).

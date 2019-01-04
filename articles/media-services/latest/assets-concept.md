---
title: Tillgångar i Media Services – Azure | Microsoft Docs
description: Den här artikeln innehåller en förklaring av vad tillgångar är och hur de används av Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/01/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 8507d51f0d4d49d89fc24b38ed73df7488261daa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969583"
---
# <a name="assets"></a>Tillgångar

I Azure Media Services, en [tillgången](https://docs.microsoft.com/rest/api/media/assets) innehåller digitala filer (inklusive video, ljud, bilder, miniatyrsamlingar, textspår och filer med dold textning) och metadata om dessa filer. När de digitala filerna överförs till en tillgång, kan de användas i Media Services encoding, strömning, analysera innehållet arbetsflöden. Mer information finns i den [ladda upp digitala filer till tillgångar](#upload-digital-files-into-assets) nedan.

En tillgång är mappad till en blobbehållare i den [Azure Storage-konto](storage-account-concept.md) och filer i tillgången lagras som blockblobar i den behållaren. Media Services stöder Blob nivåerna när kontot använder för generell användning v2 (GPv2) lagring. Med GPv2, kan du flytta filer till [lågfrekvent lagring eller Arkivlagring](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **Arkivera** lagring är lämplig för arkivering av källfilerna när det inte längre behövs (till exempel när de har kodats).

Den **Arkiv** lagringsnivå rekommenderas endast för mycket stora källfiler som redan har kodats och kodning jobbutdata placerades i en utdata-blob-behållare. Blobar i behållaren för utdata som du vill associera med en tillgång och Använd för att strömma eller analysera ditt innehåll måste finnas i en **frekvent** eller **lågfrekvent** lagringsnivå.

## <a name="asset-definition"></a>Definition av tillgången

I följande tabell visar tillgångens egenskaper och ger definitionerna.

|Namn|Beskrivning|
|---|---|
|id|Fullständigt kvalificerade resurs-ID för resursen.|
|namn|Namnet på resursen.|
|properties.alternateId |Alternativa ID för tillgången.|
|properties.assetId |Plats-ID.|
|Properties.container |Namnet på blob-behållare för tillgången.|
|Properties.Created |Skapandedatum för tillgången.<br/> Datetime är alltid i UTC-format.|
|properties.description|Beskrivning för tillgången.|
|properties.lastModified |Senaste ändringsdatum för tillgången. <br/> Datetime är alltid i UTC-format.|
|properties.storageAccountName |Namnet på lagringskontot.|
|properties.storageEncryptionFormat |Krypteringsformat tillgången. En NONE eller MediaStorageEncryption.|
|typ|Typ av resursen.|

En fullständig definition finns [tillgångar](https://docs.microsoft.com/rest/api/media/assets).

## <a name="upload-digital-files-into-assets"></a>Ladda upp digitala filer till tillgångar

En av de vanliga arbetsflödena för Media Services är att överföra, koda och överföra en fil. Det här avsnittet beskrivs de allmänna stegen.

1. Använd Media Services v3-API för att skapa en ny ”in” tillgång. Den här åtgärden skapar en behållare i lagringskontot som associerats med Media Services-kontot. API: et returnerar behållarens namn (till exempel `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    Om du redan har en blob-behållare som du vill associera med en tillgång kan du ange behållarens namn när du skapar tillgången. Media Services stöder för närvarande endast blobbar i behållaren roten och inte med sökvägar i filnamnet. Därför fungerar en behållare med namnet ”input.mp4”. En behållare med filnamnet ”videos/inputs/input.mp4” fungerar dock inte.

    Du kan använda Azure CLI för att ladda upp direkt till lagringskontot och behållaren som du har rättigheter till i din prenumeration. <br/>Behållarens namn måste vara unikt och Följ riktlinjerna för namngivning lagring. Namnet behöver inte följa Media Services tillgången behållarens namn (tillgångar-GUID) formatering. 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Hämta en SAS-URL med läs-och behörigheter som används för att ladda upp digitala filer till tillgången behållaren. Du kan använda Media Services-API till [lista över URL: er för tillgången behållare](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Använda Azure Storage API: er eller SDK: er (till exempel den [Storage REST API](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), eller [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) att ladda upp filer till tillgången behållaren. 
4. Använd Media Services v3-API: er för att skapa en transformering och ett jobb för att bearbeta din ”in” tillgång. Mer information finns i [transformeringar och jobb](transform-concept.md).
5. Stream innehållet från ”utdatatillgången”.

> [!TIP]
> För en fullständig .NET-exempel som visar hur du: skapa tillgången, få en skrivbar SAS-URL till den tillgången behållare i storage, ladda upp filen till behållaren i storage med SAS-Webbadressen, se [skapa en jobbindata från en lokal fil](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Skapa en ny tillgång

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

En REST-exempel finns i den [skapa en tillgång med REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) exempel.

Exemplet visar hur du skapar den **Begärandetext** där du kan ange användbar information som beskrivning, behållarnamn, storage-konto och annan information.

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

Ett fullständigt exempel se [skapa en jobbindata från en lokal fil](job-input-from-local-file-how-to.md). I Media Services v3, en jobbindata kan även skapa från HTTPS-URL: er (se [skapa en jobbindata från en HTTPS-URL](job-input-from-http-how-to.md)).

## <a name="filtering-ordering-paging"></a>Filtrering, skrivordning, växling

Media Services har stöd för följande OData-frågealternativ för tillgångar: 

* $filter 
* $orderby 
* $top 
* $skiptoken 

Operatorn beskrivning:

* EQ = lika med
* Ne = inte lika med
* Ge = större än eller lika med
* Le = mindre än eller lika med
* Gt = större än
* Lt = mindre än

### <a name="filteringordering"></a>Filtrering/ordning

I följande tabell visar hur dessa alternativ kan tillkomma för tillgången egenskaper: 

|Namn|Filter|Beställa|
|---|---|---|
|id|||
|namn|Stöder: Eq, Gt, Lt|Stöder: Stigande och fallande|
|properties.alternateId |Stöder: EQ||
|properties.assetId |Stöder: EQ||
|Properties.container |||
|Properties.Created|Stöder: Eq, Gt, Lt| Stöder: Stigande och fallande|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|typ|||

I följande C#-exempel filtrerar på Skapad datum:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>Sidbrytning

Sidbrytning stöds för var och en av fyra aktiverade sorteringsordningar. Sidstorleken är för närvarande 1 000.

> [!TIP]
> Du bör alltid använda nästa länk för att räkna upp samlingen och inte är beroende av en viss storlek.

Om ett frågesvar innehåller många objekt, tjänsten returnerar en ”\@odata.nextLink” egenskapen för att hämta nästa sida i resultatet. Detta kan användas för att bläddra igenom hela resultatmängden. Du kan inte konfigurera sidstorleken. 

Om tillgångar skapas eller tas bort vid bläddring genom samlingen, visas ändringarna i de returnerade resultaten (om dessa ändringar finns i en del av den samling som inte har hämtats). 

#### <a name="c-example"></a>C#-exempel

I följande C#-exempel visar hur du räknar upp via alla tillgångar i kontot.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

#### <a name="rest-example"></a>REST-exempel

Överväg följande exempel visar där $skiptoken används. Kontrollera att du ersätter *amstestaccount* med ditt kontonamn och ange den *api-versionen* värde till den senaste versionen.

Om du begär en lista över tillgångar så här:

```
GET  https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Du skulle få tillbaka ett svar ungefär så här:

```
HTTP/1.1 200 OK
 
{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-5a4f-470a-9d81-6037d7c23eff","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-98d07299-5a4f-470a-9d81-6037d7c23eff","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-912e-447b-a1ed-0f723913b20d","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-fd05a503-912e-447b-a1ed-0f723913b20d","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Du kan sedan begära nästa sida genom att skicka en get-begäran:

```
https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

Fler REST-exempel finns i [tillgångar - listan](https://docs.microsoft.com/rest/api/media/assets/list)

## <a name="storage-side-encryption"></a>Kryptering för lagring på serversidan

Resurserna som ska krypteras av kryptering för lagring på serversidan för att skydda dina tillgångar i vila. I följande tabell visar hur kryptering för lagring på serversidan fungerar i Media Services:

|Krypteringsalternativet|Beskrivning|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services-Lagringskryptering|AES-256-kryptering, viktiga hanteras av Media Services|Stöd för<sup>(1)</sup>|Stöds inte<sup>(2)</sup>|
|[Kryptering av lagringstjänst för vilande Data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Kryptering på serversidan som erbjuds av Azure Storage, nyckel hanteras av Azure eller av kunden|Stöds|Stöds|
|[Storage Client Side Encryption](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Client side encryption som erbjuds av Azure storage, nyckel som hanteras av kunden i Key Vault|Stöds inte|Stöds inte|

<sup>1</sup> medan Media Services har stöd för hantering av innehållet i klartext/utan någon form av kryptering, göra så rekommenderas inte.

<sup>2</sup> i Media Services v3 lagringskryptering (AES-256-kryptering) är bara stöds för bakåtkompatibilitet när dina tillgångar skapades med Media Services v2. Vilket innebär att v3 fungerar med befintliga lagring krypteras tillgångar, men tillåter inte skapandet av nya.

## <a name="next-steps"></a>Nästa steg

[Strömma en fil](stream-files-dotnet-quickstart.md)

[Skillnader mellan Media Services v2 och v3](migrate-from-v2-to-v3.md)

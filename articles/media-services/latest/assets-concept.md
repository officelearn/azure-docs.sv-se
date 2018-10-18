---
title: Tillgångar i Azure Media Services | Microsoft Docs
description: Den här artikeln innehåller en förklaring av vad tillgångar är och hur de används av Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: fcb4500a1e4503d90b00528544ae98fa93e16191
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49379234"
---
# <a name="assets"></a>Tillgångar

En **tillgången** innehåller digitala filer (inklusive video, ljud, bilder, miniatyrsamlingar, textspår och filer med dold textning) och metadata om dessa filer. När de digitala filerna överförs till en tillgång, kan de användas i Media Services kodning och strömning arbetsflöden.

En tillgång är mappad till en blobbehållare i den [Azure Storage-konto](storage-account-concept.md) och filer i tillgången lagras som blockblobar i den behållaren. Du kan interagera med tillgångsfiler i behållare med hjälp av Storage SDK-klienter.

Azure Media Services stöder Blob nivåerna när kontot använder för generell användning v2 (GPv2) lagring. Med GPv2, kan du flytta filer till lågfrekvent eller kall lagring. Kall lagring är lämplig för arkivering av källfilerna när det inte längre behövs (till exempel när de har kodats.)

I Media Services v3 kan jobbindata skapas från tillgångar eller från http (s)-URL: er. Om du vill skapa en tillgång som kan användas som indata för jobbet [skapa en jobbindata från en lokal fil](job-input-from-local-file-how-to.md).

Läs även om [storage-konton i Media Services](storage-account-concept.md) och [transformeringar och jobb](transform-concept.md).

## <a name="asset-definition"></a>Definition av tillgången

I följande tabell visar tillgångens egenskaper och ger definitionerna.

|Namn|Typ|Beskrivning|
|---|---|---|
|id|sträng|Fullständigt kvalificerade resurs-ID för resursen.|
|namn|sträng|Namnet på resursen.|
|properties.alternateId |sträng|Alternativa ID för tillgången.|
|properties.assetId |sträng|Plats-ID.|
|Properties.container |sträng|Namnet på blob-behållare för tillgången.|
|Properties.Created |sträng|Skapandedatum för tillgången.|
|properties.description |sträng|Beskrivning för tillgången.|
|properties.lastModified |sträng|Senaste ändringsdatum för tillgången.|
|properties.storageAccountName |sträng|Namnet på lagringskontot.|
|properties.storageEncryptionFormat |AssetStorageEncryptionFormat |Krypteringsformat tillgången. En NONE eller MediaStorageEncryption.|
|typ|sträng|Typ av resursen.|

Läs den fullständiga definitionen [tillgångar](https://docs.microsoft.com/rest/api/media/assets).

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
|namn|Har stöd för: Eq, Gt, Lt|Stöder: stigande och fallande|
|properties.alternateId |Stöder: Eq||
|properties.assetId |Stöder: Eq||
|Properties.container |||
|Properties.Created|Har stöd för: Eq, Gt, Lt| Har stöd för: Stigande och fallande|
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

Om tillgångar skapas eller tas bort vid bläddring genom samlingen, syns ändringarna i de returnerade resultaten (om dessa ändringar finns i en del av den samling som inte har hämtats.) 

I följande C#-exempel visar hur du räknar upp via alla tillgångar i kontot.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

REST-exempel finns i [tillgångar - listan](https://docs.microsoft.com/rest/api/media/assets/assets_list)


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

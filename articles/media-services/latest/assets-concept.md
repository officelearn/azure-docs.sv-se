---
title: Tillgångar i Azure Media Services | Microsoft Docs
description: Den här artikeln ger en förklaring av tillgångar är och hur de används i Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 61555eb6cca6995215ce43051abbda9aa43539ec
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284846"
---
# <a name="assets"></a>Tillgångar

En **tillgången** innehåller digitala filer (inklusive video, ljud, bilder, miniatyrsamlingar, textspår och filer med dold textning) och metadata om dessa filer. När digitala filer överförs till en tillgång, kan de användas i Media Services encoding och direktöverföring av arbetsflöden.

En tillgång som är mappad till en blobbbehållare i den [Azure Storage-konto](storage-account-concept.md) och filerna i tillgången lagras som blockblobar i behållaren. Du kan interagera med tillgångsfiler i behållare med lagring SDK-klienter.

Azure Media Services stöder Blob nivåerna när kontot använder allmänna v2 (GPv2) lagring. Med GPv2, kan du flytta filer att kyla ner eller kall lagringsplats. Kalla lagring är lämplig för arkivering källfilerna när det inte längre behövs (till exempel när de har kodats.)

I Media Services v3 skapas indata jobb från tillgångar eller från http (s)-URL: er. Om du vill skapa en tillgång som kan användas som indata för jobbet finns [skapar ett jobb indata från en lokal fil](job-input-from-local-file-how-to.md).

Läs om [storage-konton i Media Services](storage-account-concept.md) och [transformeringar och jobb](transform-concept.md).

## <a name="asset-definition"></a>Definition av tillgångsinformation

I följande tabell visas tillgångens egenskaper och ger deras definitioner.

|Namn|Typ|Beskrivning|
|---|---|---|
|Id|sträng|Fullständiga resurs-ID för resursen.|
|namn|sträng|Namnet på resursen.|
|properties.alternateId |sträng|Alternativt ID för tillgången.|
|properties.assetId |sträng|Tillgångsinformation-ID.|
|Properties.container |sträng|Namnet på blob-behållaren tillgången.|
|Properties.Created |sträng|Skapandedatum för tillgången.|
|properties.description |sträng|Beskrivning för tillgången.|
|properties.lastModified |sträng|Senaste ändringsdatum för tillgången.|
|properties.storageAccountName |sträng|Namnet på lagringskontot.|
|properties.storageEncryptionFormat |AssetStorageEncryptionFormat |Tillgångsinformation krypteringsformat. En NONE eller MediaStorageEncryption.|
|typ|sträng|Typ av resursen.|

Se för en fullständig definition [tillgångar](https://docs.microsoft.com/rest/api/media/assets).

## <a name="filtering-ordering-paging"></a>Filtrera, sortera, sidindelning

Media Services stöder följande alternativ för OData-frågan för tillgångar: 

* $filter 
* $orderby 
* $top 
* $skiptoken 

### <a name="filteringordering"></a>Filtrering/ordning

Följande tabell visar hur dessa alternativ kan tillämpas på Egenskaper för tillgångsinformation: 

|Namn|Filter|Ordning|
|---|---|---|
|Id|Stöder:<br/>Lika med<br/>Större än<br/>Mindre än|Stöder:<br/>Stigande<br/>Fallande|
|namn|||
|properties.alternateId |Stöder:<br/>Lika med||
|properties.assetId |Stöder:<br/>Lika med||
|Properties.container |||
|Properties.Created|Stöder:<br/>Lika med<br/>Större än<br/>Mindre än|Stöder:<br/>Stigande<br/>Fallande|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|typ|||

Följande C#-exempel filtrerar på Skapad datum:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>Sidbrytning

Sidbrytning stöds för var och en av de fyra aktiverade sorteringsordningar. 

Om ett frågesvar innehåller många (för närvarande via 1000)-objekt, tjänsten returnerar ett ”\@odata.nextLink” egenskapen för att hämta nästa sida i resultaten. Detta kan användas för att bläddra igenom hela resultatmängden. Sidstorleken kan inte konfigureras av användaren. 

Om tillgångar skapas eller tas bort vid växling genom insamling, syns ändringarna i returnerade resultat (om ändringarna i en del av den samling som inte har hämtats.) 

Följande C#-exempel visar hur du räkna upp alla tillgångar i kontot.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

REST-exempel finns [tillgångar - lista](https://docs.microsoft.com/rest/api/media/assets/list)


## <a name="storage-side-encryption"></a>Kryptering för lagring på serversidan

För att skydda dina tillgångar vilande ska tillgångarna krypteras med kryptering för lagring på serversidan. Följande tabell visar hur sida lagringskryptering fungerar i Media Services:

|Krypteringsalternativ|Beskrivning|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services-Lagringskryptering|AES-256-kryptering, nyckel hanteras av Media Services|Stöd för<sup>(1)</sup>|Stöds inte<sup>(2)</sup>|
|[Lagringstjänstens kryptering av vilande Data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Kryptering på serversidan som erbjuds av Azure Storage, nyckel hanteras av Azure eller av kunden|Stöds|Stöds|
|[Kryptering på klientsidan](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Klientsidans kryptering som erbjuds av Azure storage, nyckel som hanteras av kunden i Key Vault|Stöds inte|Stöds inte|

<sup>1</sup> medan Media Services stöder hantering av innehållet i klartext/utan någon form av kryptering, göra så rekommenderas inte.

<sup>2</sup> i Media Services v3 lagringskryptering (AES 256-kryptering) är bara stöds för bakåtkompatibilitet när dina tillgångar har skapats med Media Services v2. Vilket innebär att v3 fungerar med befintlig lagring krypterad tillgångar, men tillåter inte att skapa nya.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Strömma en fil](stream-files-dotnet-quickstart.md)

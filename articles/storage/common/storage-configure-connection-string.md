---
title: Konfigurera en anslutningssträng
titleSuffix: Azure Storage
description: Konfigurera en anslutningssträng för ett Azure-lagringskonto. En anslutningssträng innehåller den information som behövs för att auktorisera åtkomst till ett lagringskonto från ditt program vid körning med hjälp av auktorisering för delad nyckel.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 12/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f617beec8a53570ede7755040cfbb92a7d1712b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268358"
---
# <a name="configure-azure-storage-connection-strings"></a>Konfigurera Azure Storage-anslutningssträngar

En anslutningssträng innehåller den auktoriseringsinformation som krävs för att ditt program ska komma åt data i ett Azure Storage-konto under körning med hjälp av auktorisering av delad nyckel. Du kan konfigurera anslutningssträngar till:

* Anslut till Azure-lagringsemulatorn.
* Få åtkomst till ett lagringskonto i Azure.
* Åtkomst till angivna resurser i Azure via en SIGNATUR för delad åtkomst (SAS).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-and-copy-a-connection-string"></a>Visa och kopiera en anslutningssträng

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="store-a-connection-string"></a>Lagra en anslutningssträng

Ditt program måste komma åt anslutningssträngen vid körning för att auktorisera begäranden som gjorts till Azure Storage. Du har flera alternativ för att lagra anslutningssträngen:

* Du kan lagra anslutningssträngen i en miljövariabel.
* Ett program som körs på skrivbordet eller på en enhet kan lagra anslutningssträngen i en **app.config-** eller **web.config-fil.** Lägg till anslutningssträngen i avsnittet **AppSettings** i dessa filer.
* Ett program som körs i en Azure-molntjänst kan lagra anslutningssträngen i [Azure-tjänstkonfigurationsschemafilen (.cscfg).](https://msdn.microsoft.com/library/ee758710.aspx) Lägg till anslutningssträngen i avsnittet **ConfigurationSettings** i tjänstkonfigurationsfilen.

Om du lagrar anslutningssträngen i en konfigurationsfil är det enkelt att uppdatera anslutningssträngen för att växla mellan lagringsemulatorn och ett Azure-lagringskonto i molnet. Du behöver bara redigera anslutningssträngen för att peka på din målmiljö.

Du kan använda [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) för att komma åt anslutningssträngen vid körning oavsett var ditt program körs.

## <a name="configure-a-connection-string-for-the-storage-emulator"></a>Konfigurera en anslutningssträng för lagringsemulatorn

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Mer information om lagringsemulatorn finns i [Använda Azure-lagringsemulatorn för utveckling och testning](storage-use-emulator.md).

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Konfigurera en anslutningssträng för ett Azure-lagringskonto

Om du vill skapa en anslutningssträng för ditt Azure-lagringskonto använder du följande format. Ange om du vill ansluta till lagringskontot via `myAccountName` HTTPS (rekommenderas) eller HTTP, `myAccountKey` ersätta med namnet på ditt lagringskonto och ersätta med din kontoåtkomstnyckel:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Anslutningssträngen kan till exempel se ut ungefär som:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Även om Azure Storage stöder både HTTP och HTTPS i en *anslutningssträng rekommenderas HTTPS.*

> [!TIP]
> Du hittar anslutningssträngarna för ditt lagringskonto i [Azure-portalen](https://portal.azure.com). Navigera till INSTÄLLNINGAR > **Access-tangenterna** i ditt lagringskontos menyblad för att se anslutningssträngar för både primära och sekundära åtkomstnycklar. **SETTINGS**
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Skapa en anslutningssträng med hjälp av en signatur för delad åtkomst

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Skapa en anslutningssträng för en explicit lagringsslutpunkt

Du kan ange explicita tjänstslutpunkter i anslutningssträngen i stället för att använda standardslutpunkterna. Om du vill skapa en anslutningssträng som anger en explicit slutpunkt anger du den fullständiga tjänstslutpunkten för varje tjänst, inklusive protokollspecifikationen (HTTPS (rekommenderas) eller HTTP, i följande format:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Ett scenario där du kanske vill ange en explicit slutpunkt är när du har mappat slutpunkten för Blob-lagring till en [anpassad domän](../blobs/storage-custom-domain-name.md). I så fall kan du ange din anpassade slutpunkt för Blob-lagring i anslutningssträngen. Du kan också ange standardslutpunkter för de andra tjänsterna om programmet använder dem.

Här är ett exempel på en anslutningssträng som anger en explicit slutpunkt för Blob-tjänsten:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

I det här exemplet anges explicita slutpunkter för alla tjänster, inklusive en anpassad domän för Blob-tjänsten:

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

Slutpunktsvärdena i en anslutningssträng används för att konstruera uri:erna för begäran till lagringstjänsterna och diktera formen på eventuella URI:er som returneras till koden.

Om du har mappat en lagringsslutpunkt till en anpassad domän och utelämnat slutpunkten från en anslutningssträng, kan du inte använda anslutningssträngen för att komma åt data i den tjänsten från koden.

> [!IMPORTANT]
> Tjänstslutpunktsvärden i anslutningssträngarna måste vara välformade URI:er, inklusive `https://` (rekommenderas) eller `http://`. Eftersom Azure Storage ännu inte stöder HTTPS *must* för `http://` anpassade domäner måste du ange för alla slutpunkts-URI som pekar på en anpassad domän.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Skapa en anslutningssträng med ett slutpunktssuffix

Om du vill skapa en anslutningssträng för en lagringstjänst i regioner eller instanser med olika slutpunktssuffix, till exempel för Azure China 21Vianet eller Azure Government, använder du följande anslutningssträngformat. Ange om du vill ansluta till lagringskontot via `myAccountName` HTTPS (rekommenderas) eller HTTP, ersätta med namnet på ditt lagringskonto, ersätta `myAccountKey` med din kontoåtkomstnyckel och ersätta `mySuffix` med URI-suffixet:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Här är en exempelanslutningssträng för lagringstjänster i Azure China 21Vianet:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Tolka en anslutningssträng

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Nästa steg

* [Använd Azure-lagringsemulatorn för utveckling och testning](storage-use-emulator.md)
* [Utforskare för Azure Storage](storage-explorers.md)
* [Använda SIGNATURER för delad åtkomst (SAS)](storage-sas-overview.md)

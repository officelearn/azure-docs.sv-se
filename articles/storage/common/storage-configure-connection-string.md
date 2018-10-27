---
title: Konfigurera en anslutningssträng för Azure Storage | Microsoft Docs
description: Konfigurera en anslutningssträng för ett Azure storage-konto. En anslutningssträng innehåller den information som behövs för att bevilja åtkomst till ett lagringskonto från ditt program under körning.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/12/2017
ms.author: tamram
ms.component: common
ms.openlocfilehash: 75f8e5605b84b21abf92cd77a71793dbceb4a62b
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139062"
---
# <a name="configure-azure-storage-connection-strings"></a>Konfigurera Azure Storage-anslutningssträngar

En anslutningssträng innehåller autentiseringsinformation som krävs för ditt program för att komma åt data i ett Azure Storage-konto vid körning. Du kan konfigurera anslutningssträngar för att:

* Anslut till Azure storage-emulatorn.
* Åtkomst till ett lagringskonto i Azure.
* Åtkomst till resurserna i Azure via en signatur för delad åtkomst (SAS).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Lagra din anslutningssträng
Programmet behöver för att få åtkomst till anslutningssträngen vid körning för att godkänna begäranden som görs till Azure Storage. Har du flera alternativ för att lagra anslutningssträngen:

* Ett program som körs på skrivbordet eller på en enhet kan lagra anslutningssträngen i en **app.config** eller **web.config** fil. Lägg till anslutningssträng till den **AppSettings** avsnitt i dessa filer.
* Ett program som körs i en Azure-molntjänst kan lagra anslutningssträngen i den [Azure schema (.cscfg) för tjänstkonfigurationsfilen](https://msdn.microsoft.com/library/ee758710.aspx). Lägg till anslutningssträng till den **ConfigurationSettings** avsnittet i tjänstekonfigurationsfilen.
* Du kan använda anslutningssträngen direkt i din kod. Vi rekommenderar dock att du lagrar anslutningssträngen i en konfigurationsfil i de flesta fall.

Lagra anslutningssträngen i en konfigurationsfil gör det enkelt att uppdatera anslutningssträngen för att växla mellan lagringsemulatorn och ett Azure storage-konto i molnet. Du behöver bara redigera anslutningssträngen så att den pekar till målmiljön.

Du kan använda den [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) att komma åt din anslutningssträngen vid körningstillfället oavsett var ditt program körs.

## <a name="create-a-connection-string-for-the-storage-emulator"></a>Skapa en anslutningssträng för storage-emulatorn
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Läs mer om storage-emulatorn [använder Azure storage-emulatorn för utveckling och testning](storage-use-emulator.md).

## <a name="create-a-connection-string-for-an-azure-storage-account"></a>Skapa en anslutningssträng för ett Azure storage-konto
Använd följande format för att skapa en anslutningssträng för Azure storage-kontot. Ange om du vill ansluta till storage-konto via HTTPS (rekommenderas) eller HTTP, Ersätt `myAccountName` med namnet på ditt lagringskonto och Ersätt `myAccountKey` med din åtkomstnyckel:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Anslutningssträngen kan till exempel se ut ungefär som:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Även om Azure Storage stöder både HTTP och HTTPS i en anslutningssträng *HTTPS rekommenderas starkt*.

> [!TIP]
> Du kan hitta lagringskontots anslutningssträngar i den [Azure-portalen](https://portal.azure.com). Gå till **inställningar** > **åtkomstnycklar** i ditt storage-konto appmenyns blad i anslutningssträngar för båda primära och sekundära åtkomstnycklar.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Skapa en anslutningssträng med hjälp av en signatur för delad åtkomst
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Skapa en anslutningssträng för en explicit storage-slutpunkt
Du kan ange explicita tjänstslutpunkter i anslutningssträngen istället för att använda standard-slutpunkter. Om du vill skapa en anslutningssträng som anger en explicit slutpunkt, anger du fullständiga tjänst-slutpunkt för varje tjänst, inklusive protokollspecifikation (HTTPS (rekommenderas) eller HTTP) i följande format:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Ett scenario där du kanske vill ange en explicit slutpunkt är när du har anslutit din Blob storage-slutpunkt för en [anpassad domän](../blobs/storage-custom-domain-name.md). I så fall kan du ange din anpassade slutpunkt för Blob storage i anslutningssträngen. Alternativt kan du ange standardslutpunkterna för andra tjänster om du använder dem för ditt program.

Här är ett exempel på en anslutningssträng som anger en explicit slutpunkt för Blob-tjänsten:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Det här exemplet anges explicit slutpunkter för alla tjänster, inklusive en anpassad domän för Blob-tjänsten:

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

Slutpunkt-värden i en anslutningssträng används för att skapa begäran URI: er till storage-tjänster och bestämmer form av eventuella URI: er som returneras till din kod.

Om du har mappat en slutpunkt för lagring till en anpassad domän och utelämna att slutpunkten från en anslutningssträng, kommer sedan du inte att kunna använda denna anslutningssträng att komma åt data i tjänsten från din kod.

> [!IMPORTANT]
> Tjänsten endpoint värdena i anslutningssträngarna måste vara korrekt strukturerad URI: er, inklusive `https://` (rekommenderas) eller `http://`. Eftersom Azure Storage inte stöder ännu HTTPS för egna domäner du *måste* ange `http://` för valfri slutpunkt URI som pekar på en anpassad domän.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Skapa en anslutningssträng med slutpunktssuffixet
Om du vill skapa en anslutningssträng för en lagringstjänst i regioner eller instanser med olika endpoint suffix, till exempel för Azure Kina eller Azure Government kan du använda följande formatet för anslutningssträngen. Ange om du vill ansluta till storage-konto via HTTPS (rekommenderas) eller HTTP, Ersätt `myAccountName` med namnet på ditt lagringskonto, Ersätt `myAccountKey` med din åtkomstnyckel och Ersätt `mySuffix` med URI-suffix:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Här är en exempel-anslutningssträng för storage-tjänster i Azure i Kina:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Parsning av en anslutningssträng
[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Nästa steg
* [Använd Azure storage-emulatorn för utveckling och testning](storage-use-emulator.md)
* [Azure Storage-Utforskare](storage-explorers.md)
* [Använda signaturer för delad åtkomst (SAS)](storage-dotnet-shared-access-signature-part-1.md)


---
title: Konfigurera en anslutningssträng för Azure Storage | Microsoft Docs
description: Konfigurera en anslutningssträng för ett Azure storage-konto. En anslutningssträng innehåller den information som krävs för att autentisera åtkomst till ett lagringskonto från ditt program vid körning.
services: storage
documentationcenter: ''
author: craigshoemaker
manager: jeconnoc
editor: tysonn
ms.assetid: ecb0acb5-90a9-4eb2-93e6-e9860eda5e53
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: cshoe
ms.openlocfilehash: dee0fd68ccaf80ff6e44ee10fa24bb19e220d0d5
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
ms.locfileid: "30240837"
---
# <a name="configure-azure-storage-connection-strings"></a>Konfigurera Azure Storage-anslutningssträngar

En anslutningssträng innehåller autentiseringsinformation som krävs för ditt program för att komma åt data i Azure Storage-konto vid körning. Du kan konfigurera anslutningssträngar för att:

* Ansluta till Azure storage-emulatorn.
* Åtkomst till ett lagringskonto i Azure.
* Åtkomst till angivna resurser i Azure via en signatur för delad åtkomst (SAS).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Lagra anslutningssträngen
Ditt program behöver åtkomst till anslutningssträngen vid körningstillfället att autentisera begäranden till Azure Storage. Du har flera alternativ för att lagra anslutningssträngen:

* Ett program som körs på skrivbordet eller på en enhet kan lagra anslutningssträngen i en **app.config** eller **web.config** fil. Lägg till anslutningssträngen till den **AppSettings** avsnitt i dessa filer.
* Ett program som körs i en Azure-molntjänst kan lagra anslutningssträngen i den [Azure-tjänstkonfigurationsfil schema (.cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Lägg till anslutningssträngen till den **ConfigurationSettings** i tjänstkonfigurationsfil.
* Du kan använda anslutningssträngen direkt i din kod. Vi rekommenderar dock att du lagrar anslutningssträngen i en konfigurationsfil i de flesta fall.

Spara anslutningssträngen i en konfigurationsfil gör det enkelt att uppdatera anslutningssträngen att växla mellan storage-emulatorn och ett Azure storage-konto i molnet. Behöver du bara redigera anslutningssträng att peka målmiljön.

Du kan använda den [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) att komma åt anslutningssträngen vid körningstillfället oavsett där programmet körs.

## <a name="create-a-connection-string-for-the-storage-emulator"></a>Skapa en anslutningssträng för storage-emulatorn
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Mer information om storage-emulatorn finns [Använd Azure storage-emulatorn för utveckling och testning](storage-use-emulator.md).

## <a name="create-a-connection-string-for-an-azure-storage-account"></a>Skapa en anslutningssträng för ett Azure storage-konto
Använd följande format för att skapa en anslutningssträng för Azure storage-konto. Ange om du vill ansluta till storage-konto via HTTPS (rekommenderas) eller HTTP ersätter `myAccountName` med namnet på ditt lagringskonto och Ersätt `myAccountKey` med din åtkomstnyckel:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Anslutningssträngen kan till exempel se ut:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Azure Storage har stöd för både HTTP och HTTPS i en anslutningssträng *HTTPS rekommenderas*.

> [!TIP]
> Du kan hitta ditt lagringskonto anslutningssträngar i den [Azure-portalen](https://portal.azure.com). Gå till **inställningar** > **åtkomstnycklar** i ditt lagringskonto menyn bladet för att visa anslutningssträngar för båda primära och sekundära nycklarna.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Skapa en anslutningssträng med hjälp av en signatur för delad åtkomst
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Skapa en anslutningssträng för en explicit lagring-slutpunkt
Du kan ange Tjänsteslutpunkter explicit i anslutningssträngen i stället för standard-slutpunkter. Ange den fullständiga tjänstslutpunkten för varje tjänst, inklusive protokollspecifikation (HTTPS (rekommenderas) eller HTTP) i följande format för att skapa en anslutningssträng som anger en explicit slutpunkt:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Ett scenario där du kanske vill ange en explicit slutpunkt är när du har anslutit din slutpunkt för Blob-lagring till en [domänen](../blobs/storage-custom-domain-name.md). I så fall kan du ange din anpassade slutpunkt för Blob storage i anslutningssträngen. Du kan också ange standardslutpunkterna för andra tjänster om programmet använder dem.

Här är ett exempel på en anslutningssträng som anger en explicit slutpunkt för Blob-tjänsten:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Det här exemplet anger explicit slutpunkter för alla tjänster, inklusive en anpassad domän för Blob-tjänsten:

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

Slutpunkten värdena i en anslutningssträng används för att konstruera begäran URI: er till storage-tjänster och styr form av alla URI: er som returneras till din kod.

Om du har anslutit en slutpunkt för lagring till en anpassad domän och utesluter den slutpunkten från en anslutningssträng kommer sedan du inte att kunna använda den anslutningssträngen att komma åt data i tjänsten från din kod.

> [!IMPORTANT]
> Tjänsten endpoint värdena i anslutningssträngarna måste vara giltig URI: er, inklusive `https://` (rekommenderas) eller `http://`. Eftersom Azure Storage inte stöder ännu HTTPS för anpassade domäner du *måste* ange `http://` för valfri slutpunkt URI som pekar på en anpassad domän.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Skapa en anslutningssträng med en slutpunkt-suffix
Om du vill skapa en anslutningssträng för en lagringstjänst i regioner eller instanser med olika endpoint suffix, till exempel för Azure Kina eller Azure Government använder du följande connection-strängformat. Ange om du vill ansluta till storage-konto via HTTPS (rekommenderas) eller HTTP ersätter `myAccountName` med namnet på ditt lagringskonto, Ersätt `myAccountKey` med din åtkomstnyckel och Ersätt `mySuffix` med suffixet URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Här är en exempel-anslutningssträng för storage-tjänster i Azure Kina:

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
* [Azure lagringsutforskare](storage-explorers.md)
* [Använda signaturer för delad åtkomst (SAS)](storage-dotnet-shared-access-signature-part-1.md)


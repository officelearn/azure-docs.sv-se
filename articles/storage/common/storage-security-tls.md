---
title: Aktivera säker TLS för Azure Storage-klienten | Microsoft Docs
description: Lär dig hur du aktiverar TLS 1.2 i Azure Storage-klienten.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/25/2018
ms.author: fryu
ms.openlocfilehash: 5c21df2b3bdeee6ac7c3956fe1cafa4f947dd6dd
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036435"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Aktivera säker TLS för Azure Storage-klient

När du behöver granska dina tjänster med Azure Storage baserat på senaste kompatibilitet och säkerhetskrav, SSL 1.0, 2.0, 3.0 och TLS 1.0 identifieras som icke-kompatibla kommunikationsprotokoll.

SSL version 1.0, 2.0 och 3.0 har befunnits vara utsatt. De har förbjudits av RFC. TLS 1.0 blir osäker för användning av osäker blockchiffer (DES CBC och RC2 CBC) och dataströmmen cipher (RC4). PCI council föreslog migreringen till högre TLS-version. Mer information hittar du [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

Azure Storage har stoppats SSL 3.0 sedan 2015 och använder TLS 1.2 på offentliga HTTPs-slutpunkter men TLS 1.0- och TLS 1.1 som fortfarande stöds för bakåtkompatibilitet.

För att säkerställa säker och kompatibla anslutningen till Azure Storage, måste du Aktivera TLS 1.2 på klienten innan du skickar begäranden till fungerar Azure Storage-tjänst.

## <a name="enable-tls-12-in-net-client"></a>Aktivera TLS 1.2 i .NET-klienten

Både behöver stöd för TLS 1.2 för klienten att förhandla TLS 1.2, Operativsystemet och .NET Framework-version. Se mer information i [stöd för TLS 1.2](https://docs.microsoft.com/en-us/dotnet/framework/network-programming/tls#support-for-tls-12).

I följande exempel visas hur du aktiverar TLS 1.2 i .NET-klienten.

```csharp

    static void EnableTls12()
    {
        // Enable TLS 1.2 before connecting to Azure Storage
        System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

        // Connect to Azure Storage
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName={yourstorageaccount};AccountKey={yourstorageaccountkey};EndpointSuffix=core.windows.net");
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        CloudBlobContainer container = blobClient.GetContainerReference("foo");
        container.CreateIfNotExists();
    }

```

## <a name="enable-tls-12-in-powershell-client"></a>Aktivera TLS 1.2 i PowerShell-klienten

I följande exempel visas hur du aktiverar TLS 1.2 i PowerShell-klienten.

```powershell

# Enable TLS 1.2 before connecting to Azure Storage
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

$resourceGroup = "{YourResourceGropuName}"
$storageAccountName = "{YourStorageAccountNme}"
$prefix = "foo"

# Connect to Azure Storage
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccountName
$ctx = $storageAccount.Context
$listOfContainers = Get-AzureStorageContainer -Context $ctx -Prefix $prefix
$listOfContainers

```

## <a name="verify-tls-12-connection"></a>Verifiera TLS 1.2-anslutning

Du kan använda Fiddler för att kontrollera om TLS 1.2 är faktiskt används. Öppna Fiddler att börja samla in nätverkstrafik för klienten och sedan köra ovanstående exempel. Sedan hittar TLS-version i den anslutning som gör att provet.

Följande skärmbild visas ett exempel för verifiering.

![Skärmbild av verifiera TLS version i Fiddler](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Se också

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)

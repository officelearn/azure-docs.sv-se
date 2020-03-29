---
title: Aktivera säker TLS med .NET
titleSuffix: Azure Storage
description: Lär dig hur du aktiverar TLS 1.2 med hjälp av .NET-klientbiblioteket för Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 81c9a8fe9513f1f8fc65ad64b34f0fb04383569b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371810"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Aktivera säker TLS för Azure Storage-klient

TLS (Transport Layer Security) och SSL (Secure Sockets Layer) är kryptografiska protokoll som ger kommunikationssäkerhet över ett datornätverk. SSL 1.0, 2.0 och 3.0 har visat sig vara sårbara. De har förbjudits av RFC. TLS 1.0 blir osäker för användning av osäkert blockchiffer (DES CBC och RC2 CBC) och Stream chiffer (RC4). PCI rådet föreslog också övergången till högre TLS versioner. Mer information finns i [TLS (Transport Layer Security).](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)

Azure Storage har stoppat SSL 3.0 sedan 2015 och använder TLS 1.2 på offentliga HTTPs-slutpunkter, men TLS 1.0 och TLS 1.1 stöds fortfarande för bakåtkompatibilitet.

För att säkerställa säker och kompatibel anslutning till Azure Storage måste du aktivera TLS 1.2 eller nyare version på klientsidan innan du skickar begäranden om att driva Azure Storage-tjänsten.

## <a name="enable-tls-12-in-net-client"></a>Aktivera TLS 1.2 i .NET-klienten

För att klienten ska kunna förhandla om TLS 1.2 måste operativsystemet och .NET Framework-versionen ha stöd för TLS 1.2. Se mer information i [Support för TLS 1.2](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).

Följande exempel visar hur du aktiverar TLS 1.2 i .NET-klienten.

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

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)] 

Följande exempel visar hur du aktiverar TLS 1.2 i PowerShell-klienten.

```powershell
# Enable TLS 1.2 before connecting to Azure Storage
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

$resourceGroup = "{YourResourceGroupName}"
$storageAccountName = "{YourStorageAccountNme}"
$prefix = "foo"

# Connect to Azure Storage
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccountName
$ctx = $storageAccount.Context
$listOfContainers = Get-AzStorageContainer -Context $ctx -Prefix $prefix
$listOfContainers
```

## <a name="verify-tls-12-connection"></a>Verifiera TLS 1.2-anslutning

Du kan använda Fiddler för att kontrollera om TLS 1.2 används faktiskt. Öppna Fiddler för att börja fånga klientnätverkstrafik och kör sedan ovanstående exempel. Sedan kan du hitta TLS-versionen i den anslutning som exemplet gör.

Följande skärmbild är ett exempel på verifieringen.

![skärmdump av att verifiera TLS-version i Fiddler](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Se även

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [PCI-kompatibilitet på TLS](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls)
* [Aktivera TLS i Java-klienten](https://www.java.com/en/configure_crypto.html)

---
title: Aktivera säker TLS med .NET
titleSuffix: Azure Storage
description: Lär dig hur du aktiverar TLS 1,2 med hjälp av .NET-klient biblioteket för Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 81c9a8fe9513f1f8fc65ad64b34f0fb04383569b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "75371810"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Aktivera säker TLS för Azure Storage-klient

Transport Layer Security (TLS) och Secure Sockets Layer (SSL) är krypterings protokoll som ger kommunikations säkerhet i ett dator nätverk. SSL 1,0, 2,0 och 3,0 har befunnits vara sårbara. De har tillåtits av RFC. TLS 1,0 blir osäkert vid användning av osäkert block chiffer (DES CBC och RC2 CBC) och Stream chiffer (RC4). PCI-rådet föreslog även migreringen till högre TLS-versioner. För mer information, kan du se [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

Azure Storage har stoppat SSL 3,0 sedan 2015 och använder TLS 1,2 på offentliga HTTPs-slutpunkter, men TLS 1,0 och TLS 1,1 stöds fortfarande för bakåtkompatibilitet.

För att säkerställa en säker och kompatibel anslutning till Azure Storage måste du Aktivera TLS 1,2 eller nyare version på klient sidan innan du skickar begär Anden för att fungera Azure Storage-tjänsten.

## <a name="enable-tls-12-in-net-client"></a>Aktivera TLS 1.2 i .NET-klienten

För att klienten ska kunna förhandla TLS 1,2 måste operativ systemet och .NET Framework-versionen ha stöd för TLS 1,2. Se mer information i [stöd för TLS 1,2](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).

I följande exempel visas hur du aktiverar TLS 1,2 i .NET-klienten.

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

I följande exempel visas hur du aktiverar TLS 1,2 i PowerShell-klienten.

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

## <a name="verify-tls-12-connection"></a>Verifiera TLS 1,2-anslutning

Du kan använda Fiddler för att kontrol lera om TLS 1,2 används faktiskt. Öppna Fiddler för att börja samla in klient nätverks trafik och kör sedan exemplet ovan. Sedan kan du hitta TLS-versionen i den anslutning som exemplet gör.

Följande skärm bild är ett exempel på verifieringen.

![skärm bild av verifiering av TLS-version i Fiddler](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Se även

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [PCI-kompatibilitet på TLS](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls)
* [Aktivera TLS i Java-klient](https://www.java.com/en/configure_crypto.html)

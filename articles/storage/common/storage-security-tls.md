---
title: Aktivera säker TLS för Azure Storage-klienten | Microsoft Docs
description: Lär dig hur du aktiverar TLS 1.2 i Azure Storage-klienten.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/25/2018
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 218708ffc9a680150d7b6bf559a00ca87054bbe8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65152975"
---
# <a name="enable-secure-tls-for-azure-storage-client"></a>Aktivera säker TLS för Azure Storage-klient

Transport Layer Security (TLS) och Secure Sockets Layer (SSL) är kryptografiska protokoll som tillhandahåller KOMMUNIKATIONSSÄKERHET via ett datornätverk. SSL 1.0, 2.0 och 3.0 har hittats sårbara. De har förbjudits av RFC. TLS 1.0 blir osäkert för att använda osäkert blockchiffer (DES CBC och RC2 CBC) och Stream cipher (RC4). PCI-rådet föreslås också migreringen till högre TLS-version. Mer information kan du se [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0).

Azure Storage har stoppats SSL 3.0 sedan 2015 och använder TLS 1.2 på offentliga HTTPs-slutpunkter, men TLS 1.0 och TLS 1.1 kan fortfarande användas för bakåtkompatibilitet.

För att säkerställa säker och kompatibel anslutning till Azure Storage kan behöva du Aktivera TLS 1.2 eller senare version på klientsidan innan du skickar begäranden om att använda Azure Storage-tjänsten.

## <a name="enable-tls-12-in-net-client"></a>Aktivera TLS 1.2 på .NET-klient

För att klienten kan förhandla TLS 1.2, Operativsystemet och .NET Framework-version måste både du stöd för TLS 1.2. Se mer information finns i [stöd för TLS 1.2](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).

I följande exempel visas hur du aktiverar TLS 1.2 .NET-klienten.

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

## <a name="enable-tls-12-in-powershell-client"></a>Aktivera TLS 1.2 i PowerShell-klient

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)] 

I följande exempel visas hur du aktiverar TLS 1.2 PowerShell-klienten.

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

## <a name="verify-tls-12-connection"></a>Verifiera TLS 1.2-anslutningen

Du kan använda Fiddler för att verifiera om TLS 1.2 används faktiskt. Öppna Fiddler för att börja samla in nätverkstrafik för klienten och sedan köra ovan exemplet. Sedan kan du hitta TLS-version i den anslutning som gör provet.

Följande skärmbild visas ett exempel för verifiering.

![Skärmbild med att verifiera TLS-version i Fiddler](./media/storage-security-tls/storage-security-tls-verify-in-fiddler.png)

## <a name="see-also"></a>Se också

* [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)
* [PCI-efterlevnad på TLS](https://blog.pcisecuritystandards.org/migrating-from-ssl-and-early-tls)
* [Aktivera TLS i Java-klient](https://www.java.com/en/configure_crypto.html)

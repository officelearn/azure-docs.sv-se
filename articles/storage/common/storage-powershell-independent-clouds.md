---
title: Använda PowerShell för att hantera data i Azure-oberoende moln
titleSuffix: Azure Storage
description: Hantera lagring i Kina Cloud, Government Cloud och tyska molnet med Azure PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5fa515515c06466e121a5c0ee925fd4d14245363
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895241"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Hantera lagring i Azure-oberoende moln med PowerShell

De flesta använder Azure Public Cloud för sin globala Azure-distribution. Det finns också några oberoende distributioner av Microsoft Azure på grund av suveränitet och så vidare. Dessa oberoende distributioner kallas "miljöer". I följande lista beskrivs de oberoende moln som för närvarande är tillgängliga.

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [Azure China 21Vianet Cloud drivs av 21Vianet i Kina](http://www.windowsazure.cn/)
* [Azure tyska molnet](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Använda ett oberoende moln

Om du vill använda Azure Storage i ett av de oberoende molnen ansluter du till molnet i stället för Azure Public. Så här använder du ett av de oberoende molnen i stället för Azure Public:

* Du anger *vilken miljö* du vill ansluta till.
* Du bestämmer och använder de tillgängliga regionerna.
* Du använder rätt slutpunktssuffix, som skiljer sig från Azure Public.

Exemplen kräver Azure PowerShell-modul Az version 0.7 eller senare. I ett PowerShell-fönster kör du `Get-Module -ListAvailable Az` för att hitta versionen. Om ingenting finns med i listan, eller om du behöver uppgradera, läser du [Installera Azure PowerShell-modul](/powershell/azure/install-Az-ps).

## <a name="log-in-to-azure"></a>Logga in på Azure

Kör [cmdleten Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) för att se tillgängliga Azure-miljöer:

```powershell
Get-AzEnvironment
```

Logga in på ditt konto som har åtkomst till molnet som du vill ansluta och ställa in miljön till. Det här exemplet visar hur du loggar in på ett konto som använder Azure Government Cloud.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

Använd miljön **AzureChinaCloud**för att komma åt China Cloud . Använd **AzureGermanCloud**för att komma åt det tyska molnet .

Om du nu behöver listan över platser för att skapa ett lagringskonto eller en annan resurs kan du fråga de platser som är tillgängliga för det valda molnet med [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

```powershell
Get-AzLocation | select Location, DisplayName
```

I följande tabell visas de platser som returneras för det tyska molnet.

|Location | Visningsnamn |
|----|----|
| `germanycentral` | Tyskland, centrala|
| `germanynortheast` | Tyskland, nordöstra |


## <a name="endpoint-suffix"></a>Suffix för slutpunkt

Slutpunktssuffixet för var och en av dessa miljöer skiljer sig från slutpunkten för Azure Public. Till exempel är blob-slutpunktssuffixet för Azure Public **blob.core.windows.net**. För myndighetsmolnet är blob-slutpunktssuffixet **blob.core.usgovcloudapi.net**.

### <a name="get-endpoint-using-get-azenvironment"></a>Hämta slutpunkt med Get-AzEnvironment

Hämta slutpunktssuffixet med [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment). Slutpunkten är egenskapen *StorageEndpointSuffix* för miljön.

Följande kodavsnitt visar hur du hämtar slutpunktssuffixet. Alla dessa kommandon tillbaka något i stil med "core.cloudapp.net" eller "core.cloudapi.de", etc. Lägg till suffixet i lagringstjänsten för att komma åt den tjänsten. Till exempel kommer "queue.core.cloudapi.de" åt kötjänsten i tyska molnet.

Det här kodavsnittet hämtar alla miljöer och slutpunktssuffixet för var och en.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

Det här kommandot returnerar följande resultat.

| Namn| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

Om du vill hämta alla egenskaper för den angivna miljön anropar du **Get-AzEnvironment** och anger molnnamnet. Det här kodavsnittet returnerar en lista över egenskaper. leta efter **StorageEndpointSuffix** i listan. Följande exempel är för det tyska molnet.

```powershell
Get-AzEnvironment -Name AzureGermanCloud
```

Resultaten liknar följande värden:

|Egenskapsnamn|Värde|
|----|----|
| Namn | `AzureGermanCloud` |
| AktiveraAdfsAuthentication | `False` |
| ActiveDirectoryServiceEndpointResourceI | `http://management.core.cloudapi.de/` |
| GalleriURL | `https://gallery.cloudapi.de/` |
| ManagementPortalUrl | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl | `https://manage.core.cloudapi.de/` |
| PubliceraInställningarFileUrl| `https://manage.microsoftazure.de/publishsettings/index` |
| ResursManagerUrl | `http://management.microsoftazure.de/` |
| SqlDatabaseDnsSuffix | `.database.cloudapi.de` |
| **StorageEndpointSuffix** | `core.cloudapi.de` |
| ... | ... |
Om du bara vill hämta egenskapen för slutpunkt för lagring hämtar du det specifika molnet och ber om just den egenskapen.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix
```

Det här kommandot returnerar följande information:

`Storage Endpoint Suffix = core.cloudapi.de`

### <a name="get-endpoint-from-a-storage-account"></a>Hämta slutpunkt från ett lagringskonto

Du kan också undersöka egenskaperna för ett lagringskonto för att hämta slutpunkterna:

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

För ett lagringskonto i government cloud returnerar det här kommandot följande utdata:

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>När du har ställt in miljön

Härifrån framöver kan du använda samma PowerShell som används för att hantera dina lagringskonton och komma åt dataplanet som beskrivs i artikeln [Använda Azure PowerShell med Azure Storage](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat en ny resursgrupp och ett lagringskonto för den här övningen kan du ta bort båda tillgångarna genom att ta bort resursgruppen. Alla resurser som ingår i gruppen tas bort om resursgruppen tas bort.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

* [Bevara användarinloggningar mellan PowerShell-sessioner](/powershell/azure/context-persistence)
* [Lagring av Azure-myndigheter](../../azure-government/documentation-government-services-storage.md)
* [Utvecklarhandboken för Microsoft Azure Government](../../azure-government/documentation-government-developer-guide.md)
* [Utvecklaranteckningar för Azure China 21Vianet-program](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Dokumentation för Azure Tyskland](../../germany/germany-welcome.md)

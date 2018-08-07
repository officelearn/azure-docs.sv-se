---
title: Hantera lagring i Azure oberoende moln med hjälp av Azure PowerShell | Microsoft Docs
description: Hantera lagring i Kina-molnet, Government-molnet och tyska molnet med hjälp av Azure PowerShell
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 10/24/2017
ms.author: rogarana
ms.component: common
ms.openlocfilehash: f3f827958c0c5d71846d8f0d4184ef88b7f0a932
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39527412"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Hantera lagring i Azure-oberoende molnet med hjälp av PowerShell

De flesta använda Azures offentliga moln för sin globala Azure-distribution. Det finns även vissa oberoende distributioner av Microsoft Azure på grund av landsbaserad placering och så vidare. Dessa oberoende distributioner kallas ”miljöer”. I följande lista beskrivs de oberoende moln som är tillgänglig för tillfället.

* [Azure Government-molnet](https://azure.microsoft.com/features/gov/)
* [Azure Kina-molnet som drivs av 21Vianet i Kina](http://www.windowsazure.cn/)
* [Azure Tyskland-molnet](../../germany/germany-welcome.md)

## <a name="using-an-independent-cloud"></a>Med hjälp av en oberoende molnet 

För att använda Azure Storage i ett av de oberoende moln måste ansluta du till molnet i stället för offentlig Azure. Använda en av de oberoende moln i stället för offentlig Azure:

* Du anger den *miljö* som ska anslutas.
* Du avgör och använder de tillgängliga regionerna.
* Du använder rätt slutpunkt-suffix som skiljer sig från Azure offentlig.

Exemplen kräver Azure PowerShell-Modulversion 4.4.0 eller senare. Kör i ett PowerShell-fönster `Get-Module -ListAvailable AzureRM` att hitta versionen. Om det inte finns eller om du behöver uppgradera kan du se [installera Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps). 

## <a name="log-in-to-azure"></a>Logga in på Azure

Kör den [Get-AzureEnvironment](/powershell/module/azure/Get-AzureRmEnvironment) cmdlet för att se tillgängliga Azure-miljöer:
   
```powershell
Get-AzureRmEnvironment
```

Logga in på ditt konto som har åtkomst till molnet som du vill ansluta och ställer in miljön. Det här exemplet visar hur du loggar in på ett konto som använder Azure Government-molnet.   

```powershell
Connect-AzureRmAccount –Environment AzureUSGovernment
```

För att komma åt Kina-molnet, använder du miljön **AzureChinaCloud**. Du kommer åt tyska molnet genom att använda **AzureGermanCloud**.

Om du behöver listan över platser för att skapa ett lagringskonto eller en annan resurs kan du nu fråga platserna som är tillgängliga för den valda molnet med hjälp av [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation).

```powershell
Get-AzureRmLocation | select Location, DisplayName
```

I följande tabell visas de platser som returneras för det tyska molnet.

|Plats | DisplayName |
|----|----|
| germanycentral | Centrala Tyskland|
| germanynortheast | Nordöstra Tyskland | 


## <a name="endpoint-suffix"></a>Slutpunktssuffixet

Slutpunktssuffixet för var och en av dessa miljöer skiljer sig från offentlig Azure-slutpunkten. Suffix för blob-slutpunkten för offentlig Azure-är till exempel **blob.core.windows.net**. För Government-molnet suffix för blob-slutpunkten är **blob.core.usgovcloudapi.net**. 

### <a name="get-endpoint-using-get-azurermenvironment"></a>Hämta slutpunkten med hjälp av Get-AzureRMEnvironment 

Hämta slutpunkten suffix med [Get-AzureRMEnvironment](/powershell/module/azurerm.profile/get-azurermenvironment). Slutpunkten är den *StorageEndpointSuffix* egenskap för miljön. I följande kodavsnitt visar hur du gör detta. Alla dessa kommandon returnera något ”core.cloudapp.net” eller ”core.cloudapi.de”, t.ex. Lägg till detta till storage-tjänsten för att komma åt tjänsten. Till exempel kommer ”queue.core.cloudapi.de” åt kötjänsten i tyska molnet.

Det här kodfragmentet hämtar alla miljöerna och slutpunktssuffixet för var och en.

```powershell
Get-AzureRmEnvironment | select Name, StorageEndpointSuffix 
```

Det här kommandot returnerar följande resultat.

| Namn| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | Core.chinacloudapi.CN|
| AzureCloud | Core.Windows.NET |
| AzureGermanCloud | Core.cloudapi.de|
| AzureUSGovernment | Core.usgovcloudapi.NET |

Om du vill hämta alla egenskaper för den angivna miljön anropa **Get-AzureRmEnvironment** och ange namnet på molnet. Det här kodfragmentet returnerar en lista med egenskaper. Leta efter **StorageEndpointSuffix** i listan. I följande exempel är för det tyska molnet.

```powershell
Get-AzureRmEnvironment -Name AzureGermanCloud 
```

Resultatet liknar följande:

|Egenskapsnamn|Värde|
|----|----|
| Namn | AzureGermanCloud |
| EnableAdfsAuthentication | False |
| ActiveDirectoryServiceEndpointResourceI | http://management.core.cloudapi.de/ |
| GalleryURL | https://gallery.cloudapi.de/ |
| ManagementPortalUrl | https://portal.microsoftazure.de/ | 
| ServiceManagementUrl | https://manage.core.cloudapi.de/ |
| PublishSettingsFileUrl| https://manage.microsoftazure.de/publishsettings/index |
| ResourceManagerUrl | http://management.microsoftazure.de/ |
| SqlDatabaseDnsSuffix | .database.cloudapi.de |
| **StorageEndpointSuffix** | Core.cloudapi.de |
| ... | ... | 

Om du vill hämta bara storage suffix slutpunktsegenskapen hämta specifika molnet och ber om precis som en egenskap.

```powershell
$environment = Get-AzureRmEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix 
```

Det här returnerar följande information.

```
Storage Endpoint Suffix = core.cloudapi.de
```

### <a name="get-endpoint-from-a-storage-account"></a>Hämta slutpunkten från ett lagringskonto

Du kan också kontrollera egenskaperna för ett lagringskonto för att hämta slutpunkterna. Det kan vara användbart om du redan använder ett lagringskonto i din PowerShell-skript; Du kan bara hämta slutpunkten som du behöver. 

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

Det här returnerar ett lagringskonto i molnet för myndigheter, följande: 

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>När du har angett miljön

Från här framöver, du kan använda samma PowerShell används för att hantera dina lagringskonton och få åtkomst till dataplanet enligt beskrivningen i artikeln [med hjälp av Azure PowerShell med Azure Storage](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat en ny resursgrupp och ett lagringskonto för den här övningen kan du ta bort alla tillgångar genom att ta bort resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tas bort lagringskontot som skapas och själva resursgruppen.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

* [Bevara användarinloggningar mellan PowerShell-sessioner](/powershell/azure/context-persistence)
* [Azure Government-lagring](../../azure-government/documentation-government-services-storage.md)
* [Utvecklarguide för Microsoft Azure Government](../../azure-government/documentation-government-developer-guide.md)
* [Kommentarer för utvecklare för Azure Kina program](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Azure Tyskland-dokumentation](../../germany/germany-welcome.md)

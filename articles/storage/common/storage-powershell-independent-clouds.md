---
title: Hantera lagring i Azure oberoende moln med Azure PowerShell | Microsoft Docs
description: Hantera lagring i Kina molnet, offentliga moln och tyska moln med Azure PowerShell
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: rogarana
ms.openlocfilehash: 3eecf8918e6628071e44cc588b1826df68a00f10
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186927"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Hantera lagring i Azure oberoende moln med hjälp av PowerShell

De flesta använder offentliga Azure-molnet för sina globala Azure-distribution. Det finns även vissa oberoende distributioner av Microsoft Azure på grund av suveränitet och så vidare. Dessa oberoende distributioner kallas ”miljöer”. I följande lista beskrivs tillgängliga oberoende moln.

* [Azure Government-moln](https://azure.microsoft.com/features/gov/)
* [Kina Azuremolnet drivs av 21Vianet i Kina](http://www.windowsazure.cn/)
* [Tyska Azuremolnet](../../germany/germany-welcome.md)

## <a name="using-an-independent-cloud"></a>Med hjälp av ett oberoende moln 

Om du vill använda Azure Storage på ett oberoende moln, ansluta till molnet i stället för offentlig Azure. Att använda en av de oberoende moln i stället för offentlig Azure:

* Du anger den *miljö* som ska anslutas.
* Du avgör och använder tillgängliga regioner.
* Du använder rätt endpoint-suffix som skiljer sig åt från offentlig Azure.

Exemplen kräver Azure PowerShell Modulversion 4.4.0 eller senare. Kör i PowerShell-fönster `Get-Module -ListAvailable AzureRM` versionen. Om det inte finns, eller så måste du uppgradera, se [installera Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps). 

## <a name="log-in-to-azure"></a>Logga in på Azure

Kör den [Get-AzureEnvironment](/powershell/module/azure/Get-AzureRmEnvironment) för att visa tillgängliga Azure miljöer:
   
```powershell
Get-AzureRmEnvironment
```

Logga in på ditt konto som har åtkomst till molnet som du vill ansluta och ställa in miljön. Det här exemplet visar hur du logga in på ett konto som använder Azure Government-molnet.   

```powershell
Connect-AzureRmAccount –Environment AzureUSGovernment
```

Använd miljön för att komma åt Kina molnet **AzureChinaCloud**. För att komma åt tyska molnet använder **AzureGermanCloud**.

Om du behöver listan över platser för att skapa ett lagringskonto eller en annan resurs kan du nu frågar platserna som är tillgängliga för det valda molnet med hjälp av [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation).

```powershell
Get-AzureRmLocation | select Location, DisplayName
```

I följande tabell visas de platser som returneras för tyska molnet.

|Plats | Visningsnamn |
|----|----|
| germanycentral | Centrala Tyskland|
| germanynortheast | Nordöstra Tyskland | 


## <a name="endpoint-suffix"></a>Suffix för slutpunkten

Slutpunkten suffix för var och en av dessa miljöer skiljer sig från Azure offentlig slutpunkt. Suffix för blob-slutpunkten för offentlig Azure är till exempel **blob.core.windows.net**. För det offentliga molnet suffixet blob-slutpunkten är **blob.core.usgovcloudapi.net**. 

### <a name="get-endpoint-using-get-azurermenvironment"></a>Hämta slutpunkten med hjälp av Get-AzureRMEnvironment 

Hämta suffix slutpunkten med [Get-AzureRMEnvironment](/powershell/module/azurerm.profile/get-azurermenvironment). Slutpunkten är den *StorageEndpointSuffix* -egenskapen för miljön. Följande kodavsnitt visar hur du gör detta. Alla de här kommandona returnerar något ”core.cloudapp.net” eller ”core.cloudapi.de” osv. Lägg till detta till storage-tjänst för att komma åt tjänsten. Till exempel kommer ”queue.core.cloudapi.de” åt kötjänsten i tyska molnet.

Det här kodstycket hämtar alla miljöerna och suffixet slutpunkt för varje kriterium.

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

Om du vill hämta alla egenskaper för den angivna miljön anropa **Get-AzureRmEnvironment** och ange namnet på det moln. Det här kodstycket returnerar en lista med egenskaper. Leta efter **StorageEndpointSuffix** i listan. I följande exempel är för tyska molnet.

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

Om du vill hämta bara lagring suffix slutpunktsegenskapen hämta specifika molnet och frågar efter precis som en egenskap.

```powershell
$environment = Get-AzureRmEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix 
```

Detta returnerar följande information.

```
Storage Endpoint Suffix = core.cloudapi.de
```

### <a name="get-endpoint-from-a-storage-account"></a>Hämta endpoint från ett lagringskonto

Du kan också kontrollera egenskaperna för ett lagringskonto för att hämta slutpunkterna. Det kan vara användbart om du redan använder ett lagringskonto i PowerShell-skript; Du kan bara hämta den slutpunkt som du behöver. 

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

För ett lagringskonto i det offentliga molnet returneras följande: 

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>När du har angett i miljön

Från här framöver, kan du använda samma PowerShell används för att hantera dina lagringskonton och åtkomst till dataplan som beskrivs i artikel [med hjälp av Azure PowerShell med Azure Storage](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat en ny resursgrupp och ett lagringskonto för den här övningen kan du ta bort alla tillgångar genom att ta bort resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet den tar bort lagringskonto som skapats och resursgruppen sig själv.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

* [Spara användarinloggningar i PowerShell-sessioner](/powershell/azure/context-persistence)
* [Azure Government-lagring](../../azure-government/documentation-government-services-storage.md)
* [Utvecklarhandbok för Microsoft Azure Government](../../azure-government/documentation-government-developer-guide.md)
* [Kommentarer för utvecklare för Azure Kina program](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Azure Tyskland dokumentation](../../germany/germany-welcome.md)

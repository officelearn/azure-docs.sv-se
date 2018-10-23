---
title: Komma igång med PowerShell för Azure Batch | Microsoft Docs
description: En snabb introduktion till Azure PowerShell-cmdlets som du kan använda för att hantera Batch-resurserna.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 10/05/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6d05cb2fbee1f171daa3b73caab57a0b6acebfad
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116116"
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Hantera Batch-resurser med PowerShell-cmdletar

Med Azure Batch PowerShell-cmdletarna kan du genomföra och skriva många av de uppgifter som du utför med Batch-API:erna, Azure-portalen och Azures kommandoradsgränssnitt (CLI). Den här artikeln är en snabb introduktion till de cmdletar som du kan använda för att hantera Batch-konton och arbeta med Batch-resurser, t.ex. pooler, jobb och uppgifter.

En fullständig lista över alla Batch-cmdlets och en detaljerad cmdlet-syntax finns i [Cmdlet-referens för Azure Batch](/powershell/module/azurerm.batch/#batch).

Den har artikeln baseras på cmdletar i Azure Batch-modulen 4.1.5. Vi rekommenderar att du uppdaterar Azure PowerShell-moduler ofta för att dra nytta av tjänstuppdateringar och förbättringar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Installera och konfigurera Azure PowerShell-modulen](/powershell/azure/overview). Information om hur du installerar en viss Azure Batch-modul, till exempel en förhandsversion av en modul, finns i [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureRM.Batch/5.0.0-preview). 

* Kör cmdleten **Connect-AzureRmAccount** för att ansluta till din prenumeration (Azure Batch-cmdletar medföljer Azure Resource Manager-modulen):

  ```PowerShell
  Connect-AzureRmAccount
  ```

* **Registrera med Batch-leverantörens namnområde**. Du behöver bara utför den här åtgärden **en gång per prenumeration**.
  
  ```PowerShell
  Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`
  ```

## <a name="manage-batch-accounts-and-keys"></a>Hantera Batch-konton och nycklar

### <a name="create-a-batch-account"></a>Skapa ett Batch-konto

**New-AzureRmBatchAccount** skapar ett Batch-konto i en angiven resursgrupp. Om du inte redan har en resursgrupp, skapa en genom att köra cmdlet:en [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Ange ett av Azure-områdena i parametern **Plats**, till exempel "USA, centrala". Exempel:

```PowerShell
New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

Skapa sedan ett Batch-konto i resursgruppen och ange ett namn för kontot i <*account_name*> och din resursgrupps namn och plats. Det kan ta en stund innan skapandet av Batch-kontot har slutförts. Exempel:

```PowerShell
New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>
```

> [!NOTE]
> Batch-kontonamnet måste vara unikt för Azure-regionen för resursgruppen, innehålla mellan 3 och 24 tecken och endast bestå av små bokstäver och siffror.
> 

### <a name="get-account-access-keys"></a>Hämta kontoåtkomstnycklar

**Get-AzureRmBatchAccountKeys** visar åtkomstnycklarna som är associerade med ett Azure Batch-konto. Kör till exempel följande för att hämta de primära och sekundära nycklarna för det konto som du skapade.

 ```PowerShell
$Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### <a name="generate-a-new-access-key"></a>Generera en ny åtkomstnyckel

**New-AzureRmBatchAccountKey** genererar en ny primär eller sekundär kontonyckel för ett Azure Batch-konto. Om du till exempel vill skapa en ny primär nyckel för Batch-kontot skriver du:

```PowerShell
New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [!NOTE]
> Om du vill skapa en ny sekundär nyckel anger du ”Secondary” för parametern **KeyType**. Du måste återskapa de primära och sekundära nycklarna separat.
> 

### <a name="delete-a-batch-account"></a>Ta bort ett Batch-konto

**Remove-AzureRmBatchAccount** tar bort ett Batch-konto. Exempel:

```PowerShell
Remove-AzureRmBatchAccount -AccountName <account_name>
```

När du uppmanas att göra det bekräftar du att du vill ta bort kontot. Det kan ta en stund innan kontot tagits bort.

## <a name="create-a-batchaccountcontext-object"></a>Skapa ett BatchAccountContext-objekt

Du kan autentisera för att hantera Batch-resurser med hjälp av delad nyckel-autentisering eller Azure Active Directory-autentisering. Om du vill autentisera med hjälp av Batch PowerShell-cmdletar skapar du förs ett BatchAccountContext-objekt för att lagra dina kontouppgifter eller din identitet. Du skickar BatchAccountContext-objektet till cmdlets som använder parametern **BatchContext**. 

### <a name="shared-key-authentication"></a>Autentisering med delad nyckel

```PowerShell
$context = Get-AzureRmBatchAccountKeys -AccountName <account_name>
```

> [!NOTE]
> Som standard används kontots primära nyckel för autentisering, men du kan uttryckligen välja vilken nyckel som ska användas genom att ändra BatchAccountContext-objektets **KeyInUse**-egenskap: `$context.KeyInUse = "Secondary"`.
> 

### <a name="azure-active-directory-authentication"></a>Azure Active Directory-autentisering

```PowerShell
$context = Get-AzureRmBatchAccount -AccountName <account_name>
```

## <a name="create-and-modify-batch-resources"></a>Skapa och ändra Batch-resurser
Skapa resurser under ett Batch-konto genom att använda cmdletar som **New-AzureBatchPool**, **New-AzureBatchJob** och **New-AzureBatchTask**. Det finns motsvarande cmdlets för **Get-** och **Set-** som du kan använda för att uppdatera egenskaperna för befintliga resurser, och cmdlets för **Remove-** som du använder om du vill ta bort resurser under ett Batch-konto.

När du använder många av dessa cmdletar måste du, förutom att skicka ett BatchContext-objekt, skapa eller skicka objekt som innehåller detaljerade resursinställningar, så som visas i följande exempel. Visa den detaljerade hjälpinformationen för respektive cmdlet om du vill ha fler exempel.

### <a name="create-a-batch-pool"></a>Skapa en Batch-pool

När du skapar eller uppdaterar en Batch-pool väljer du antingen en molntjänstkonfiguration eller en konfiguration för virtuell dator för beräkningsnodernas operativsystem (se [Översikt över Batch-funktioner](batch-api-basics.md#pool)). Om du anger konfigurationen för molntjänsterna avbildas dina beräkningsnoder med någon av [versionerna av Azures gäst-OS](../cloud-services/cloud-services-guestos-update-matrix.md#releases). Om du anger konfigurationen för den virtuella datorn kan du antingen ange någon av VM-avbildningarna som stöds av Linux eller Windows som anges på [Azure Virtual Machines Marketplace][vm_marketplace] eller ange en anpassad avbildning som du har förberett.

När du kör **New-AzureBatchPool**, så överför operativsystemsinställningarna i en PSCloudServiceConfiguration eller ett PSVirtualMachineConfiguration-objekt. Till exempel skapar följande kodavsnitt en ny Batch-pool med beräkningsnoder i storlek Standard_A1 i konfigurationen för virtuell dator, avbildad med Ubuntu Server 16.04-LTS. Här anger parametern **VirtualMachineConfiguration** variabeln *$configuration* som PSVirtualMachineConfiguration-objekt. Parametern **BatchContext** anger en tidigare definierad variabel, *$context*, som BatchAccountContext-objektet.

```PowerShell
$imageRef = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("UbuntuServer","Canonical","16.04.0-LTS")

$configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageRef, "batch.node.ubuntu 16.04")

New-AzureBatchPool -Id "mypool" -VirtualMachineSize "Standard_a1" -VirtualMachineConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context
```

Beräkningsnodernas målantal i den nya poolen bestäms av en autoskalningsformel. I det här fallet är formeln helt enkelt **$TargetDedicated=4**, vilket indikerar att antalet beräkningsnoder i poolen som mest är 4.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Fråga avseende pooler, jobb, uppgifter och annan information

Använd cmdlets som **Get-AzureBatchPool**, **Get-AzureBatchJob** och **Get-AzureBatchTask** om du vill fråga efter entiteter som skapats under ett Batch-konto.

### <a name="query-for-data"></a>Fråga efter data

Använd till exempel **Get-AzureBatchPools** om du vill hämta dina pooler. Som standard returneras alla pooler i ditt konto, förutsatt att du redan har lagrat BatchAccountContext-objektet i *$context*:

```PowerShell
Get-AzureBatchPool -BatchContext $context
```

### <a name="use-an-odata-filter"></a>Använda ett OData-filter

Du kan lägga till ett OData-filter med parametern **Filter** om du bara vill söka efter de objekt som du är intresserad av. Du kan till exempel hämta alla pooler med ID:n som börjar med ”myPool”:

```PowerShell
$filter = "startswith(id,'myPool')"

Get-AzureBatchPool -Filter $filter -BatchContext $context
```

Den här metoden är inte lika flexibel som ”Where-Object”-metoden i en lokal pipeline. Frågan skickas dock direkt till Batch-tjänsten så att all filtrering utförs på serversidan, vilket sparar Internetbandbredd.

### <a name="use-the-id-parameter"></a>Använda Id-parametern

Ett alternativ till ett OData-filter är att använda parametern **Id**. Så här frågar du efter en specifik pool med ID:t ”myPool”:

```PowerShell
Get-AzureBatchPool -Id "myPool" -BatchContext $context
```

Parametern **Id** stöder endast sökningar efter fullständiga ID:n, inte jokertecken eller OData-filter.

### <a name="use-the-maxcount-parameter"></a>Använda parametern MaxCount

Som standard returnerar varje cmdlet högst 1 000 objekt. Om du når den här gränsen kan du antingen förfina filtret så att färre objekt returneras eller uttryckligen ställa in ett högsta antal med parametern **MaxCount**. Exempel:

```PowerShell
Get-AzureBatchTask -MaxCount 2500 -BatchContext $context
```

Om du vill ta bort den övre gränsen anger du **MaxCount** till 0 eller mindre.

### <a name="use-the-powershell-pipeline"></a>Använd PowerShell pipeline

Batch-cmdlets kan utnyttja PowerShell-pipelinen för att skicka data mellan cmdlets. Detta har samma effekt som om du anger en parameter men gör det enklare att arbeta med flera entiteter.

Exempelvis söka efter och visa alla aktiviteter på ditt konto:

```PowerShell
Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context
```

Starta om varje beräkningsnod i en pool:

```PowerShell
Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context
```

## <a name="application-package-management"></a>Hantera programpaket

Programpaket är ett förenklat sätt att distribuera program till beräkningsnoder i dina pooler. Med Batch-PowerShell-cmdlet:ar kan du överföra och hantera programpaket i Batch-kontot och distribuera paketversioner för att beräkna noder.

**Skapa** ett program:

```PowerShell
New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

**Lägg till** ett programpaket:

```PowerShell
New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip
```

Ange **standardversionen** för programmet:

```PowerShell
Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"
```

**Lista** ett programs paket

```PowerShell
$application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

$application.ApplicationPackages
```

**Ta bort** ett programpaket

```PowerShell
Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"
```

**Ta bort** ett program

```PowerShell
Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

> [!NOTE]
> Du måste ta bort alla programpaketversioner innan du tar bort ett program. Du får ett konfliktfel om du försöker ta bort ett program som för närvarande har programpaket.
> 

### <a name="deploy-an-application-package"></a>Distribuera ett programpaket

Du kan ange ett eller flera programpaket för distribution när du skapar en pool. När du anger ett paket när poolen skapas distribueras den till varje nod när noden ansluter till poolen. Paket distribueras också när en nod startas om eller när en avbildning återställs.

Ange alternativet `-ApplicationPackageReference` när du skapar en pool för att distribuera ett programpaket till poolen noder efterhand som de ansluts till poolen. Skapa först ett **PSApplicationPackageReference**-objekt och konfigurera det med program-Id och den paketversion som du vill distribuera till i poolens beräkningsnoder:

```PowerShell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "1.0"
```

Skapa nu poolen och ange paketreferensobjekt som argument till `ApplicationPackageReferences`-alternativet:

```PowerShell
New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference
```

Mer information om programpaket finns i [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Distribuera program till beräkningsnoder med Batch-programpaket).

> [!IMPORTANT]
> Du måste [koppla ett Azure Storage-konto](#linked-storage-account-autostorage) till Batch-kontot för att använda programpaket.
> 
> 

### <a name="update-a-pools-application-packages"></a>Uppdatera programpaket för en pool

Om du vill uppdatera program som är tilldelade till en befintlig pool skapar du ett PSApplicationPackageReference-objekt med egenskaperna (program-Id och paketversion):

```PowerShell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "2.0"

```

Sedan hämtar du poolen från Batch, tar bort eventuella befintliga paket, lägger till vår nya paketreferens och uppdaterat Batch-tjänsten med de nya poolinställningarna:

```PowerShell
$pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

$pool.ApplicationPackageReferences.Clear()

$pool.ApplicationPackageReferences.Add($appPackageReference)

Set-AzureBatchPool -BatchContext $context -Pool $pool
```

Poolens egenskaper i Batch-tjänsten har nu uppdaterats. Du måste starta om eller återställa avbildningen av noder om du vill distribuera nya programpaket för beräkningsnoder i poolen. Du kan starta om varje nod i en pool med det här kommandot:

```PowerShell
Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context
```

> [!TIP]
> Du kan distribuera flera programpaket till beräkningsnoder i poolen. Om du vill *lägga till* ett programpaket i stället för att ersätta de för närvarande distribuerade paketen, utelämnar du raden `$pool.ApplicationPackageReferences.Clear()` ovan.
> 
> 

## <a name="next-steps"></a>Nästa steg

* Detaljerad cmdlet-syntax och exempel finns i [Cmdlet-referens för Azure Batch](/powershell/module/azurerm.batch/#batch).
* Mer information om program och programpaket i Batch finns i [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Distribuera program till beräkningsnoder med Batch-programpaket).

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

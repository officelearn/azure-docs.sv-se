---
title: Komma igång med PowerShell – Azure Batch | Microsoft Docs
description: En snabb introduktion till Azure PowerShell-cmdlets som du kan använda för att hantera Batch-resurserna.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 01/15/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 3ceb7585a26290985cd5a2c523ad8094b1d1a40a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194244"
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Hantera Batch-resurser med PowerShell-cmdletar

Med Azure Batch PowerShell-cmdletarna kan du genomföra och skriva många av de uppgifter som du utför med Batch-API:erna, Azure-portalen och Azures kommandoradsgränssnitt (CLI). Det här är en snabb introduktion till de cmdletar som du kan använda för att hantera Batch-konton och arbeta med Batch-resurser, t.ex. pooler, jobb och uppgifter.

En fullständig lista över alla Batch-cmdlets och en detaljerad cmdlet-syntax finns i [Cmdlet-referens för Azure Batch](/powershell/module/az.batch).

Den har artikeln baseras på cmdletar i Azure Batch-modulen 1.0.0. Vi rekommenderar att du uppdaterar Azure PowerShell-moduler ofta för att dra nytta av tjänstuppdateringar och förbättringar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Installera och konfigurera Azure PowerShell-modulen](/powershell/azure/overview). Information om hur du installerar en viss Azure Batch-modul, till exempel en förhandsversion av en modul, finns i [PowerShell-galleriet](https://www.powershellgallery.com/packages/Az.Batch/1.0.0).

* Kör cmdleten **Connect-AzAccount** för att ansluta till din prenumeration (Azure Batch-cmdletar medföljer Azure Resource Manager-modulen):

  ```PowerShell
  Connect-AzAccount
  ```

* **Registrera med Batch-leverantörens namnområde**. Du behöver bara utför den här åtgärden **en gång per prenumeration**.
  
  ```PowerShell
  Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
  ```

## <a name="manage-batch-accounts-and-keys"></a>Hantera Batch-konton och nycklar

### <a name="create-a-batch-account"></a>Skapa ett Batch-konto

**New-AzBatchAccount** skapar ett Batch-konto i en angiven resursgrupp. Om du inte redan har en resursgrupp skapar du en genom att köra cmdleten [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Ange ett av Azure-områdena i parametern **Plats**, till exempel "USA, centrala". Exempel:

```PowerShell
New-AzResourceGroup –Name MyBatchResourceGroup –Location "Central US"
```

Skapa sedan ett Batch-konto i resursgruppen. Ange ett namn för kontot i <*account_name*>, och platsen och namnet för resursgruppen. Det kan ta en stund innan skapandet av Batch-kontot har slutförts. Exempel:

```PowerShell
New-AzBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>
```

> [!NOTE]
> Batch-kontonamnet måste vara unikt för Azure-regionen för resursgruppen, innehålla mellan 3 och 24 tecken och endast bestå av små bokstäver och siffror.

### <a name="get-account-access-keys"></a>Hämta kontoåtkomstnycklar

**Get-AzBatchAccountKeys** visar åtkomstnycklarna som är associerade med ett Azure Batch-konto. Kör till exempel följande för att hämta de primära och sekundära nycklarna för det konto som du skapade.

 ```PowerShell
$Account = Get-AzBatchAccountKeys –AccountName <account_name>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### <a name="generate-a-new-access-key"></a>Generera en ny åtkomstnyckel

**New-AzBatchAccountKey** genererar en ny primär eller sekundär kontonyckel för ett Azure Batch-konto. Om du till exempel vill skapa en ny primär nyckel för Batch-kontot skriver du:

```PowerShell
New-AzBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [!NOTE]
> Om du vill skapa en ny sekundär nyckel anger du ”Secondary” för parametern **KeyType**. Du måste återskapa de primära och sekundära nycklarna separat.

### <a name="delete-a-batch-account"></a>Ta bort ett Batch-konto

**Remove-AzBatchAccount** tar bort ett Batch-konto. Exempel:

```PowerShell
Remove-AzBatchAccount -AccountName <account_name>
```

När du uppmanas att göra det bekräftar du att du vill ta bort kontot. Det kan ta en stund innan kontot tagits bort.

## <a name="create-a-batchaccountcontext-object"></a>Skapa ett BatchAccountContext-objekt

Du kan autentisera för att hantera Batch-resurser med hjälp av delad nyckel-autentisering eller Azure Active Directory-autentisering. Om du vill autentisera med hjälp av Batch PowerShell-cmdletar skapar du förs ett BatchAccountContext-objekt för att lagra dina kontouppgifter eller din identitet. Du skickar BatchAccountContext-objektet till cmdlets som använder parametern **BatchContext**.

### <a name="shared-key-authentication"></a>Autentisering med delad nyckel

```PowerShell
$context = Get-AzBatchAccountKeys -AccountName <account_name>
```

> [!NOTE]
> Som standard används kontots primära nyckel för autentisering, men du kan uttryckligen välja vilken nyckel som ska användas genom att ändra BatchAccountContext-objektets **KeyInUse**-egenskap: `$context.KeyInUse = "Secondary"`.

### <a name="azure-active-directory-authentication"></a>Azure Active Directory-autentisering

```PowerShell
$context = Get-AzBatchAccount -AccountName <account_name>
```

## <a name="create-and-modify-batch-resources"></a>Skapa och ändra Batch-resurser

Använd cmdletar som **New-AzBatchPool**, **New-AzBatchJob** och **New-AzBatchTask** för att skapa resurser under ett Batch-konto. Det finns motsvarande cmdlets för **Get-** och **Set-** som du kan använda för att uppdatera egenskaperna för befintliga resurser, och cmdlets för **Remove-** som du använder om du vill ta bort resurser under ett Batch-konto.

När du använder många av dessa cmdletar måste du, förutom att skicka ett BatchContext-objekt, skapa eller skicka objekt som innehåller detaljerade resursinställningar, så som visas i följande exempel. Visa den detaljerade hjälpinformationen för respektive cmdlet om du vill ha fler exempel.

### <a name="create-a-batch-pool"></a>Skapa en Batch-pool

När du skapar eller uppdaterar en Batch-pool väljer du antingen en molntjänstkonfiguration eller en konfiguration för virtuell dator för beräkningsnodernas operativsystem (se [Översikt över Batch-funktioner](batch-api-basics.md#pool)). Om du anger konfigurationen för molntjänsterna avbildas dina beräkningsnoder med någon av [versionerna av Azures gäst-OS](../cloud-services/cloud-services-guestos-update-matrix.md#releases). Om du anger konfigurationen för den virtuella datorn kan du antingen ange någon av VM-avbildningarna som stöds av Linux eller Windows som anges på [Azure Virtual Machines Marketplace][vm_marketplace] eller ange en anpassad avbildning som du har förberett.

När du kör **New-AzBatchPool** skickar du operativsystemsinställningarna i ett PSCloudServiceConfiguration- eller PSVirtualMachineConfiguration-objekt. Till exempel skapar följande kodavsnitt en Batch-pool med beräkningsnoder i storleken Standard_A1 i konfigurationen för virtuella datorer, avbildade med Ubuntu Server 18.04-LTS. Här anger parametern **VirtualMachineConfiguration** variabeln *$configuration* som PSVirtualMachineConfiguration-objekt. Parametern **BatchContext** anger en tidigare definierad variabel, *$context*, som BatchAccountContext-objektet.

```PowerShell
$imageRef = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("UbuntuServer","Canonical","18.04.0-LTS")

$configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageRef, "batch.node.ubuntu 18.04")

New-AzBatchPool -Id "mypspool" -VirtualMachineSize "Standard_a1" -VirtualMachineConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context
```

Beräkningsnodernas målantal i den nya poolen beräknas med en autoskalningsformel. I det här fallet är formeln helt enkelt **$TargetDedicated=4**, vilket indikerar att antalet beräkningsnoder i poolen som mest är 4.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Fråga avseende pooler, jobb, uppgifter och annan information

Använd cmdletar som **Get-AzBatchPool**, **Get-AzBatchJob** och **Get-AzBatchTask** för att fråga efter entiteter som skapats under ett Batch-konto.

### <a name="query-for-data"></a>Fråga efter data

Använd till exempel **Get-AzBatchPools** för att hämta dina pooler. Som standard returneras alla pooler i ditt konto, förutsatt att du redan har lagrat BatchAccountContext-objektet i *$context*:

```PowerShell
Get-AzBatchPool -BatchContext $context
```

### <a name="use-an-odata-filter"></a>Använda ett OData-filter

Du kan lägga till ett OData-filter med parametern **Filter** om du bara vill söka efter de objekt som du är intresserad av. Du kan till exempel hämta alla pooler med ID:n som börjar med ”myPool”:

```PowerShell
$filter = "startswith(id,'myPool')"

Get-AzBatchPool -Filter $filter -BatchContext $context
```

Den här metoden är inte lika flexibel som ”Where-Object”-metoden i en lokal pipeline. Frågan skickas dock direkt till Batch-tjänsten så att all filtrering utförs på serversidan, vilket sparar Internetbandbredd.

### <a name="use-the-id-parameter"></a>Använda Id-parametern

Ett alternativ till ett OData-filter är att använda parametern **Id**. Så här frågar du efter en specifik pool med ID:t ”myPool”:

```PowerShell
Get-AzBatchPool -Id "myPool" -BatchContext $context
```

Parametern **Id** stöder endast sökningar efter fullständiga ID:n, inte jokertecken eller OData-filter.

### <a name="use-the-maxcount-parameter"></a>Använda parametern MaxCount

Som standard returnerar varje cmdlet högst 1 000 objekt. Om du når den här gränsen kan du antingen förfina filtret så att färre objekt returneras eller uttryckligen ställa in ett högsta antal med parametern **MaxCount**. Exempel:

```PowerShell
Get-AzBatchTask -MaxCount 2500 -BatchContext $context
```

Om du vill ta bort den övre gränsen anger du **MaxCount** till 0 eller mindre.

### <a name="use-the-powershell-pipeline"></a>Använd PowerShell pipeline

Batch-cmdletar använder PowerShell-pipelinen för att skicka data mellan cmdletar. Detta har samma effekt som om du anger en parameter men gör det enklare att arbeta med flera entiteter.

Exempelvis söka efter och visa alla aktiviteter på ditt konto:

```PowerShell
Get-AzBatchJob -BatchContext $context | Get-AzBatchTask -BatchContext $context
```

Starta om varje beräkningsnod i en pool:

```PowerShell
Get-AzBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

## <a name="application-package-management"></a>Hantera programpaket

Programpaket är ett förenklat sätt att distribuera program till beräkningsnoder i dina pooler. Med Batch-PowerShell-cmdlet:ar kan du överföra och hantera programpaket i Batch-kontot och distribuera paketversioner för att beräkna noder.

**Skapa** ett program:

```PowerShell
New-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

**Lägg till** ett programpaket:

```PowerShell
New-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip
```

Ange **standardversionen** för programmet:

```PowerShell
Set-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"
```

**Lista** ett programs paket

```PowerShell
$application = Get-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

$application.ApplicationPackages
```

**Ta bort** ett programpaket

```PowerShell
Remove-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"
```

**Ta bort** ett program

```PowerShell
Remove-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

> [!NOTE]
> Du måste ta bort alla programpaketversioner innan du tar bort ett program. Du får ett konfliktfel om du försöker ta bort ett program som för närvarande har programpaket.

### <a name="deploy-an-application-package"></a>Distribuera ett programpaket

Du kan ange ett eller flera programpaket för distribution när du skapar en pool. När du anger ett paket när poolen skapas distribueras den till varje nod när noden ansluter till poolen. Paket distribueras också när en nod startas om eller när en avbildning återställs.

Ange alternativet `-ApplicationPackageReference` när du skapar en pool för att distribuera ett programpaket till poolen noder efterhand som de ansluts till poolen. Skapa först ett **PSApplicationPackageReference**-objekt och konfigurera det med program-ID:t och den paketversion som du vill distribuera till poolens beräkningsnoder:

```PowerShell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "1.0"
```

Skapa nu poolen och ange paketreferensobjekt som argument till `ApplicationPackageReferences`-alternativet:

```PowerShell
New-AzBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference
```

Mer information om programpaket finns i [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Distribuera program till beräkningsnoder med Batch-programpaket).

> [!IMPORTANT]
> Du måste [koppla ett Azure Storage-konto](#linked-storage-account-autostorage) till Batch-kontot för att använda programpaket.

### <a name="update-a-pools-application-packages"></a>Uppdatera programpaket för en pool

Om du vill uppdatera program som är tilldelade till en befintlig pool skapar du ett PSApplicationPackageReference-objekt med önskade egenskaper (program-ID och paketversion):

```PowerShell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "2.0"

```

Sedan hämtar du poolen från Batch, tar bort eventuella befintliga paket, lägger till vår nya paketreferens och uppdaterat Batch-tjänsten med de nya poolinställningarna:

```PowerShell
$pool = Get-AzBatchPool -BatchContext $context -Id "PoolWithAppPackage"

$pool.ApplicationPackageReferences.Clear()

$pool.ApplicationPackageReferences.Add($appPackageReference)

Set-AzBatchPool -BatchContext $context -Pool $pool
```

Poolens egenskaper i Batch-tjänsten har nu uppdaterats. Du måste starta om eller återställa avbildningen av noder om du vill distribuera nya programpaket för beräkningsnoder i poolen. Du kan starta om varje nod i en pool med det här kommandot:

```PowerShell
Get-AzBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

> [!TIP]
> Du kan distribuera flera programpaket till beräkningsnoder i poolen. Om du vill *lägga till* ett programpaket i stället för att ersätta de för närvarande distribuerade paketen, utelämnar du raden `$pool.ApplicationPackageReferences.Clear()` ovan.

## <a name="next-steps"></a>Nästa steg

* Detaljerad cmdlet-syntax och exempel finns i [Cmdlet-referens för Azure Batch](/powershell/module/az.batch).
* Mer information om program och programpaket i Batch finns i [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Distribuera program till beräkningsnoder med Batch-programpaket).

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
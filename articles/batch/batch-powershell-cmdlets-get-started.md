---
title: Komma igång med PowerShell för Azure Batch | Microsoft Docs
description: En snabb introduktion till Azure PowerShell-cmdlets som du kan använda för att hantera Batch-resurserna.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: f9ad62c5-27bf-4e6b-a5bf-c5f5914e6199
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7d263dbb89b10876c3a1deb330f969c598addd6a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Hantera Batch-resurser med PowerShell-cmdletar

Med Azure Batch PowerShell-cmdletarna kan du genomföra och skriva många av de uppgifter som du utför med Batch-API:erna, Azure Portal och Azures kommandoradsgränssnitt (CLI). Det här är en snabb introduktion till de cmdletar som du kan använda för att hantera Batch-konton och arbeta med Batch-resurser, t.ex. pooler, jobb och uppgifter.

En fullständig lista över alla Batch-cmdlets och en detaljerad cmdlet-syntax finns i [Cmdlet-referens för Azure Batch](/powershell/module/azurerm.batch/#batch).

Den här artikeln baseras på cmdlet:ar i Azure PowerShell, version 3.0.0. Vi rekommenderar att du uppdaterar Azure PowerShell ofta för att dra nytta av tjänstuppdateringar och förbättringar.

## <a name="prerequisites"></a>Nödvändiga komponenter
Utför följande åtgärder för att använda Azure PowerShell för att hantera dina Batch-resurser.

* Installera och konfigurera [Azure PowerShell](/powershell/azure/overview)
* Kör cmdleten **Connect-AzureRmAccount** för att ansluta till din prenumeration (Azure Batch-cmdletar medföljer Azure Resource Manager-modulen):
  
    `Connect-AzureRmAccount`
* **Registrera med Batch-leverantörens namnområde**. Den här åtgärden behöver bara utföras **en gång per prenumeration**.
  
    `Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`

## <a name="manage-batch-accounts-and-keys"></a>Hantera Batch-konton och nycklar
### <a name="create-a-batch-account"></a>Skapa ett Batch-konto
**New-AzureRmBatchAccount** skapar ett Batch-konto i en angiven resursgrupp. Om du inte redan har en resursgrupp, skapa en genom att köra cmdlet:en [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Ange ett av Azure-områdena i parametern **Plats**, till exempel "USA, centrala". Till exempel:

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"

Skapa sedan ett Batch-konto i resursgruppen och ange ett namn för kontot i <*account_name*> och din resursgrupps namn och plats. Det kan ta en stund innan skapandet av Batch-kontot har slutförts. Till exempel:

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>

> [!NOTE]
> Batch-kontonamnet måste vara unikt för Azure-regionen för resursgruppen, innehålla mellan 3 och 24 tecken och endast bestå av små bokstäver och siffror.
> 
> 

### <a name="get-account-access-keys"></a>Hämta kontoåtkomstnycklar
**Get-AzureRmBatchAccountKeys** visar åtkomstnycklarna som är associerade med ett Azure Batch-konto. Kör till exempel följande för att hämta de primära och sekundära nycklarna för det konto som du skapade.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey

### <a name="generate-a-new-access-key"></a>Generera en ny åtkomstnyckel
**New-AzureRmBatchAccountKey** genererar en ny primär eller sekundär kontonyckel för ett Azure Batch-konto. Om du till exempel vill skapa en ny primär nyckel för Batch-kontot skriver du:

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary

> [!NOTE]
> Om du vill skapa en ny sekundär nyckel anger du ”Secondary” för parametern **KeyType**. Du måste återskapa de primära och sekundära nycklarna separat.
> 
> 

### <a name="delete-a-batch-account"></a>Ta bort ett Batch-konto
**Remove-AzureRmBatchAccount** tar bort ett Batch-konto. Till exempel:

    Remove-AzureRmBatchAccount -AccountName <account_name>

När du uppmanas att göra det bekräftar du att du vill ta bort kontot. Det kan ta en stund innan kontot tagits bort.

## <a name="create-a-batchaccountcontext-object"></a>Skapa ett BatchAccountContext-objekt
Om du vill autentisera med Batch PowerShell-cmdlets när du skapar och hanterar pooler, jobb, aktiviteter och andra resurser i Batch skapar du ett BatchAccountContext-objekt för att lagra ditt kontonamn och dina nycklar:

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

Du skickar BatchAccountContext-objektet till cmdlets som använder parametern **BatchContext**.

> [!NOTE]
> Som standard används kontots primära nyckel för autentisering, men du kan uttryckligen välja vilken nyckel som ska användas genom att ändra BatchAccountContext-objektets **KeyInUse**-egenskap: `$context.KeyInUse = "Secondary"`.
> 
> 

## <a name="create-and-modify-batch-resources"></a>Skapa och ändra Batch-resurser
Skapa resurser under ett Batch-konto genom att använda cmdletar som **New-AzureBatchPool**, **New-AzureBatchJob** och **New-AzureBatchTask**. Det finns motsvarande cmdlets för **Get-** och **Set-** som du kan använda för att uppdatera egenskaperna för befintliga resurser, och cmdlets för **Remove-** som du använder om du vill ta bort resurser under ett Batch-konto.

När du använder många av dessa cmdletar måste du, förutom att skicka ett BatchContext-objekt, skapa eller skicka objekt som innehåller detaljerade resursinställningar, så som visas i följande exempel. Visa den detaljerade hjälpinformationen för respektive cmdlet om du vill ha fler exempel.

### <a name="create-a-batch-pool"></a>Skapa en Batch-pool
När du skapar eller uppdaterar en Batch-pool väljer du antingen en molntjänstkonfiguration eller en konfiguration för virtuell dator för beräkningsnodernas operativsystem (se [Översikt över Batch-funktioner](batch-api-basics.md#pool)). Om du anger konfigurationen för molntjänsten avbildas dina beräkningsnoder med någon av [versionerna av Azures gäst-OS](../cloud-services/cloud-services-guestos-update-matrix.md#releases). Om du anger konfigurationen för den virtuella datorn kan du antingen ange någon av VM-avbildningarna som stöds av Linux eller Windows som anges på [Azure Virtual Machines Marketplace][vm_marketplace] eller ange en anpassad avbildning som du har förberett.

När du kör **New-AzureBatchPool**, så överför operativsystemsinställningarna i en PSCloudServiceConfiguration eller ett PSVirtualMachineConfiguration-objekt. Följande cmdlet skapar t.ex. en ny Batch-pool med små beräkningsnoder i molntjänstkonfigurationen, avbildade med den senaste operativsystemsversionen för familj 3 (Windows Server 2012). Här anger parametern **CloudServiceConfiguration** variabeln *$configuration* som PSCloudServiceConfiguration-objektet. Parametern **BatchContext** anger en tidigare definierad variabel, *$context*, som BatchAccountContext-objektet.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(4,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

Beräkningsnodernas målantal i den nya poolen bestäms av en autoskalningsformel. I det här fallet är formeln helt enkelt **$TargetDedicated=4**, vilket indikerar att antalet beräkningsnoder i poolen som mest är 4.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Fråga avseende pooler, jobb, uppgifter och annan information
Använd cmdlets som **Get-AzureBatchPool**, **Get-AzureBatchJob** och **Get-AzureBatchTask** om du vill fråga efter entiteter som skapats under ett Batch-konto.

### <a name="query-for-data"></a>Fråga efter data
Använd till exempel **Get-AzureBatchPools** om du vill hämta dina pooler. Som standard returneras alla pooler i ditt konto, förutsatt att du redan har lagrat BatchAccountContext-objektet i *$context*:

    Get-AzureBatchPool -BatchContext $context

### <a name="use-an-odata-filter"></a>Använda ett OData-filter
Du kan lägga till ett OData-filter med parametern **Filter** om du bara vill söka efter de objekt som du är intresserad av. Du kan till exempel hämta alla pooler med ID:n som börjar med ”myPool”:

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context

Den här metoden är inte lika flexibel som ”Where-Object”-metoden i en lokal pipeline. Frågan skickas dock direkt till Batch-tjänsten så att all filtrering utförs på serversidan, vilket sparar Internetbandbredd.

### <a name="use-the-id-parameter"></a>Använda Id-parametern
Ett alternativ till ett OData-filter är att använda parametern **Id**. Så här frågar du efter en specifik pool med ID:t ”myPool”:

    Get-AzureBatchPool -Id "myPool" -BatchContext $context

Parametern **Id** stöder endast sökningar efter fullständiga ID:n, inte jokertecken eller OData-filter.

### <a name="use-the-maxcount-parameter"></a>Använda parametern MaxCount
Som standard returnerar varje cmdlet högst 1 000 objekt. Om du når den här gränsen kan du antingen förfina filtret så att färre objekt returneras eller uttryckligen ställa in ett högsta antal med parametern **MaxCount**. Till exempel:

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Om du vill ta bort den övre gränsen anger du **MaxCount** till 0 eller mindre.

### <a name="use-the-powershell-pipeline"></a>Använd PowerShell pipeline
Batch-cmdlets kan utnyttja PowerShell-pipelinen för att skicka data mellan cmdlets. Detta har samma effekt som om du anger en parameter men gör det enklare att arbeta med flera entiteter.

Exempelvis söka efter och visa alla aktiviteter på ditt konto:

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context

Starta om varje beräkningsnod i en pool:

    Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

## <a name="application-package-management"></a>Hantera programpaket
Programpaket är ett förenklat sätt att distribuera program till beräkningsnoder i dina pooler. Med Batch-PowerShell-cmdlet:ar kan du överföra och hantera programpaket i Batch-kontot och distribuera paketversioner för att beräkna noder.

**Skapa** ett program:

    New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

**Lägg till** ett programpaket:

    New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip

Ange **standardversionen** för programmet:

    Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"

**Lista** ett programs paket

    $application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

    $application.ApplicationPackages

**Ta bort** ett programpaket

    Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"

**Ta bort** ett program

    Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

> [!NOTE]
> Du måste ta bort alla programpaketversioner innan du tar bort ett program. Du får ett konfliktfel om du försöker ta bort ett program som för närvarande har programpaket.
> 
> 

### <a name="deploy-an-application-package"></a>Distribuera ett programpaket
Du kan ange ett eller flera programpaket för distribution när du skapar en pool. När du anger ett paket när poolen skapas distribueras den till varje nod när noden ansluter till poolen. Paket distribueras också när en nod startas om eller när en avbildning återställs.

Ange alternativet `-ApplicationPackageReference` när du skapar en pool för att distribuera ett programpaket till poolen noder efterhand som de ansluts till poolen. Skapa först ett **PSApplicationPackageReference**-objekt och konfigurera det med program-Id och den paketversion som du vill distribuera till i poolens beräkningsnoder:

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "1.0"

Skapa nu poolen och ange paketreferensobjekt som argument till `ApplicationPackageReferences`-alternativet:

    New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference

Mer information om programpaket finns i [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Distribuera program till beräkningsnoder med Batch-programpaket).

> [!IMPORTANT]
> Du måste [koppla ett Azure Storage-konto](#linked-storage-account-autostorage) till Batch-kontot för att använda programpaket.
> 
> 

### <a name="update-a-pools-application-packages"></a>Uppdatera programpaket för en pool
Om du vill uppdatera program som är tilldelade till en befintlig pool skapar du ett PSApplicationPackageReference-objekt med egenskaperna (program-Id och paketversion):

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "2.0"

Sedan hämtar du poolen från Batch, tar bort eventuella befintliga paket, lägger till vår nya paketreferens och uppdaterat Batch-tjänsten med de nya poolinställningarna:

    $pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

    $pool.ApplicationPackageReferences.Clear()

    $pool.ApplicationPackageReferences.Add($appPackageReference)

    Set-AzureBatchPool -BatchContext $context -Pool $pool

Poolens egenskaper i Batch-tjänsten har nu uppdaterats. Du måste starta om eller återställa avbildningen av noder om du vill distribuera nya programpaket för beräkningsnoder i poolen. Du kan starta om varje nod i en pool med det här kommandot:

    Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

> [!TIP]
> Du kan distribuera flera programpaket till beräkningsnoder i poolen. Om du vill *lägga till* ett programpaket i stället för att ersätta de för närvarande distribuerade paketen, utelämnar du raden `$pool.ApplicationPackageReferences.Clear()` ovan.
> 
> 

## <a name="next-steps"></a>Nästa steg
* Detaljerad cmdlet-syntax och exempel finns i [Cmdlet-referens för Azure Batch](/powershell/module/azurerm.batch/#batch).
* Mer information om program och programpaket i Batch finns i [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Distribuera program till beräkningsnoder med Batch-programpaket).

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

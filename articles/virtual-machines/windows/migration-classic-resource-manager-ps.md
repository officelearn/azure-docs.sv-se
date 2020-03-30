---
title: Migrera till Resurshanteraren med PowerShell
description: Den här artikeln går igenom den plattformsstödda migreringen av IaaS-resurser, till exempel virtuella datorer, virtuella nätverk och lagringskonton från klassiska till Azure Resource Manager med hjälp av Azure PowerShell-kommandon
services: virtual-machines-windows
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 12a77c4c21a26f1ec52bb3ffdc312df56d3c4537
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249976"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>Migrera IaaS-resurser från klassiska till Azure Resource Manager med PowerShell

> [!IMPORTANT]
> Idag använder cirka 90 % av virtuella IaaS virtuella datorer [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Från och med den 28 februari 2020 har klassiska virtuella datorer föråldrats och kommer att vara helt pensionerade den 1 mars 2023. [Läs mer]( https://aka.ms/classicvmretirement) om den här utfasningen och [hur det påverkar dig](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

De här stegen visar hur du använder Azure PowerShell-kommandon för att migrera infrastruktur som en tjänst (IaaS) resurser från den klassiska distributionsmodellen till Azure Resource Manager-distributionsmodellen.

Om du vill kan du också migrera resurser med hjälp av [Azure CLI](../linux/migration-classic-resource-manager-cli.md).

* Bakgrundsinformation om migreringsscenarier som stöds finns i [Migrering av IaAS-resurser som stöds av plattformen från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Detaljerad vägledning och en övergångsgenomgång finns i [Teknisk djupdykning på migrering som stöds av plattformen från klassisk till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md).

<br>
Här är ett flödesschema för att identifiera i vilken ordning stegen måste utföras under en migreringsprocess.

![Skärmbild som visar migreringsstegen](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>Steg 1: Planera för migrering
Här är några metodtips som vi rekommenderar när du utvärderar om du vill migrera IaaS-resurser från klassiska till Resurshanteraren:

* Läs igenom [funktioner och konfigurationer som stöds och som inte stöds](migration-classic-resource-manager-overview.md). Om du har virtuella datorer som använder konfigurationer eller funktioner som inte stöds väntar du på att konfigurations- eller funktionsstödet har meddelats. Alternativt, om det passar dina behov, ta bort den funktionen eller flytta ut ur den konfigurationen för att aktivera migrering.
* Om du har automatiserade skript som distribuerar din infrastruktur och dina program idag kan du försöka skapa en liknande testkonfiguration med hjälp av skripten för migrering. Du kan också ställa in exempelmiljöer med hjälp av Azure-portalen.

> [!IMPORTANT]
> Programgateways stöds för närvarande inte för migrering från klassisk till Resurshanteraren. Om du vill migrera ett virtuellt nätverk med en programgateway tar du bort gatewayen innan du kör en Prepare-åtgärd för att flytta nätverket. När du har slutfört migreringen återansluter du gatewayen i Azure Resource Manager.
>
> Azure ExpressRoute-gateways som ansluter till ExpressRoute-kretsar i en annan prenumeration kan inte migreras automatiskt. I sådana fall tar du bort ExpressRoute-gatewayen, migrerar det virtuella nätverket och återskapar gatewayen. Mer information finns i [Migrera ExpressRoute-kretsar och associerade virtuella nätverk från klassikern till Resurshanterarens distributionsmodell](../../expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="step-2-install-the-latest-version-of-powershell"></a>Steg 2: Installera den senaste versionen av PowerShell
Det finns två huvudalternativ för att installera Azure PowerShell: [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) eller [Web Platform Installer (WebPI)](https://aka.ms/webpi-azps). WebPI tar emot månatliga uppdateringar. PowerShell Gallery tar emot uppdateringar kontinuerligt. Den här artikeln är baserad på Azure PowerShell version 2.1.0.

Installationsinstruktioner finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>Steg 3: Se till att du är administratör för prenumerationen
För att utföra den här migreringen måste du läggas till som coadministrator för prenumerationen i [Azure-portalen](https://portal.azure.com).

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Prenumeration**på **Hub-menyn** . Om du inte ser det väljer du **Alla tjänster**.
3. Leta reda på lämplig prenumerationspost och titta sedan i fältet **MIN ROLL.** För en coadministrator ska värdet vara _Kontoadministratör_.

Om du inte kan lägga till en coadministrator kontaktar du en tjänstadministratör eller coadministrator för att prenumerationen ska få ett tillagt.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Steg 4: Ställ in prenumerationen och registrera dig för migrering
Starta först en PowerShell-prompt. För migrering konfigurerar du din miljö för både klassisk och Resurshanteraren.

Logga in på ditt konto för Resource Manager-modellen.

```powershell
    Connect-AzAccount
```

Hämta tillgängliga prenumerationer med följande kommando:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Ange din Azure-prenumeration för den aktuella sessionen. I det här exemplet anges standardprenumerationsnamnet för **Min Azure-prenumeration**. Ersätt exempelprenumerationsnamnet med ditt eget.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Registrering är ett engångssteg, men du måste göra det en gång innan du försöker migrera. Utan att registrera dig visas följande felmeddelande:
>
> *BadRequest : Prenumeration registreras inte för migrering.*

Registrera dig hos migreringsresursprovidern med hjälp av följande kommando:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Vänta fem minuter tills registreringen är klar. Kontrollera godkännandets status med hjälp av följande kommando:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Kontrollera att RegistrationState `Registered` är innan du fortsätter.

Innan du växlar till den klassiska distributionsmodellen kontrollerar du att du har tillräckligt med virtuella Azure Resource Manager-virtuella dator-processorer i Azure-regionen i din nuvarande distribution eller virtuella nätverk. Du kan använda följande PowerShell-kommando för att kontrollera det aktuella antalet virtuella processorer som du har i Azure Resource Manager. Mer information om vCPU-kvoter finns i [Gränser och Azure Resource Manager](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

I det här exemplet kontrolleras tillgängligheten i regionen **västra USA.** Ersätt exempelområdesnamnet med ditt eget.

```powershell
    Get-AzVMUsage -Location "West US"
```

Logga nu in på ditt konto för den klassiska distributionsmodellen.

```powershell
    Add-AzureAccount
```

Hämta tillgängliga prenumerationer med följande kommando:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Ange din Azure-prenumeration för den aktuella sessionen. I det här exemplet anges standardprenumerationen för **Min Azure-prenumeration**. Ersätt exempelprenumerationsnamnet med ditt eget.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Steg 5: Kör kommandon för att migrera dina IaaS-resurser
* [Migrera virtuella datorer i en molntjänst (inte i ett virtuellt nätverk)](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migrera virtuella datorer i ett virtuellt nätverk](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrera ett lagringskonto](#step-52-migrate-a-storage-account)

> [!NOTE]
> Alla åtgärder som beskrivs här är idempotenta. Om du har ett annat problem än en funktion som inte stöds eller ett konfigurationsfel rekommenderar vi att du försöker förbereda, avbryta eller utföra åtgärden. Plattformen försöker sedan åtgärden igen.


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Steg 5.1: Alternativ 1 - Migrera virtuella datorer i en molntjänst (inte i ett virtuellt nätverk)
Hämta listan över molntjänster med hjälp av följande kommando. Välj sedan den molntjänst som du vill migrera. Om de virtuella datorerna i molntjänsten finns i ett virtuellt nätverk eller om de har webb- eller arbetsroller returnerar kommandot ett felmeddelande.

```powershell
    Get-AzureService | ft Servicename
```

Hämta distributionsnamnet för molntjänsten. I det här exemplet är tjänstnamnet **Min tjänst**. Ersätt exempeltjänstnamnet med ditt eget tjänstnamn.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Förbered de virtuella datorerna i molntjänsten för migrering. Du har två alternativ att välja mellan.

* **Alternativ 1: Migrera de virtuella datorerna till ett virtuellt nätverk som skapats av plattformen.**

    Kontrollera först att du kan migrera molntjänsten med hjälp av följande kommandon:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Följande kommando visar alla varningar och fel som blockerar migreringen. Om valideringen lyckas kan du gå vidare till steget Förbered.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Alternativ 2: Migrera till ett befintligt virtuellt nätverk i resurshanterarens distributionsmodell.**

    I det här exemplet anges resursgruppsnamnet till **myResourceGroup**, det virtuella nätverksnamnet till **myVirtualNetwork**och undernätsnamnet till **mySubNet**. Ersätt namnen i exemplet med namnen på dina egna resurser.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Kontrollera först att du kan migrera det virtuella nätverket med hjälp av följande kommando:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Följande kommando visar alla varningar och fel som blockerar migreringen. Om valideringen lyckas kan du fortsätta med följande Förbered steg:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

När förbereda åtgärden lyckas med något av de föregående alternativen, fråga migreringstillståndet för de virtuella datorerna. Se till att de `Prepared` är i staten.

I det här exemplet anges vm-namnet på **myVM**. Ersätt exempelnamnet med ditt eget VM-namn.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Kontrollera konfigurationen för de förberedda resurserna med hjälp av antingen PowerShell eller Azure-portalen. Om du inte är redo för migrering och vill gå tillbaka till det gamla tillståndet använder du följande kommando:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Om den förberedda konfigurationen ser bra ut kan du gå framåt och utföra resurserna med hjälp av följande kommando:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Steg 5.1: Alternativ 2 - Migrera virtuella datorer i ett virtuellt nätverk

Om du vill migrera virtuella datorer i ett virtuellt nätverk migrerar du det virtuella nätverket. De virtuella datorerna migrerar automatiskt med det virtuella nätverket. Välj det virtuella nätverk som du vill migrera.
> [!NOTE]
> [Migrera en enda virtuell dator](migrate-single-classic-to-resource-manager.md) som skapats med den klassiska distributionsmodellen genom att skapa en ny virtuell resurshanteraredator med hanterade diskar med hjälp av VHD-filerna (OS och data) på den virtuella datorn.
<br>

> [!NOTE]
> Det virtuella nätverksnamnet kan skilja sig från det som visas i den nya portalen. Den nya Azure-portalen `[vnet-name]`visar namnet som , men `Group [resource-group-name] [vnet-name]`det faktiska virtuella nätverksnamnet är av typen . Innan du startar migreringen slår du upp det `Get-AzureVnetSite | Select -Property Name` faktiska virtuella nätverksnamnet med kommandot eller visar det i den gamla Azure-portalen. 

I det här exemplet anges det virtuella nätverksnamnet på **myVnet**. Ersätt exemplets virtuella nätverksnamn med ditt eget.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Om det virtuella nätverket innehåller webb- eller arbetsroller eller virtuella datorer med konfigurationer som inte stöds visas ett felmeddelande om validering.

Kontrollera först att du kan migrera det virtuella nätverket med hjälp av följande kommando:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Följande kommando visar alla varningar och fel som blockerar migreringen. Om valideringen lyckas kan du fortsätta med följande Förbered steg:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Kontrollera konfigurationen för de förberedda virtuella datorerna med hjälp av antingen Azure PowerShell eller Azure-portalen. Om du inte är redo för migrering och vill gå tillbaka till det gamla tillståndet använder du följande kommando:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Om den förberedda konfigurationen ser bra ut kan du gå framåt och utföra resurserna med hjälp av följande kommando:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>Steg 5.2: Migrera ett lagringskonto
När du har migrerat de virtuella datorerna utför du följande nödvändiga kontroller innan du migrerar lagringskontona.

> [!NOTE]
> Om ditt lagringskonto inte har några associerade diskar eller VM-data kan du hoppa direkt till avsnittet "Validera lagringskonton och starta migreringen".

* Förutsättningskontroller om du har migrerat några virtuella datorer eller om ditt lagringskonto har diskresurser:
    * Migrera virtuella datorer vars diskar lagras i lagringskontot.

        Följande kommando returnerar rollnamn och disknamnsegenskaper för alla VM-diskar i lagringskontot. RoleName är namnet på den virtuella dator som en disk är kopplad till. Om det här kommandot returnerar diskar ska du se till att virtuella datorer som dessa diskar är anslutna till migreras innan du migrerar lagringskontot.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Ta bort obundna VM-diskar som lagras i lagringskontot.

        Hitta obundna VM-diskar i lagringskontot med hjälp av följande kommando:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Om föregående kommando returnerar diskar tar du bort diskarna med följande kommando:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * Ta bort VM-avbildningar som lagras i lagringskontot.

        Följande kommando returnerar alla VM-avbildningar med OS-diskar som lagras i lagringskontot.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         Följande kommando returnerar alla VM-avbildningar med datadiskar som lagras i lagringskontot.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Ta bort alla VM-avbildningar som returnerats av föregående kommandon med det här kommandot:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* Validera lagringskonton och starta migreringen.

    Validera varje lagringskonto för migrering med hjälp av följande kommando. I det här exemplet är lagringskontonamnet **myStorageAccount**. Ersätt exempelnamnet med namnet på ditt eget lagringskonto.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    Nästa steg är att förbereda lagringskontot för migrering.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Kontrollera konfigurationen för det förberedda lagringskontot med hjälp av Azure PowerShell eller Azure-portalen. Om du inte är redo för migrering och vill gå tillbaka till det gamla tillståndet använder du följande kommando:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Om den förberedda konfigurationen ser bra ut kan du gå framåt och utföra resurserna med hjälp av följande kommando:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Nästa steg
* [Översikt över plattformsstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd CLI för att migrera IaaS-resurser från klassiska till Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste frågorna om att migrera IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

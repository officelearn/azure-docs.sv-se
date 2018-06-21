---
title: Migrera till Resource Manager med PowerShell | Microsoft Docs
description: Den här artikeln innehåller stegvisa migreringen plattform som stöds av IaaS-resurser som virtuella datorer (VM), virtuella nätverk (Vnet) och storage-konton från klassiska till Azure Resource Manager (ARM) med hjälp av Azure PowerShell-kommandon
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 9cfdd6828a6d7ec699501a485519f843c59d0422
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295584"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migrera IaaS-resurser från klassiska till Azure Resource Manager med hjälp av Azure PowerShell
Dessa steg visar hur du använder Azure PowerShell-kommandon för att migrera infrastruktur som en tjänst (IaaS)-resurser från den klassiska distributionsmodellen till Azure Resource Manager-distributionsmodellen.

Om du vill kan du migrera resurser med hjälp av den [Azure-kommandoradsgränssnittet (Azure CLI)](../linux/migration-classic-resource-manager-cli.md).

* Bakgrundsinformation om migreringsscenarier som stöds finns i [plattform som stöds migrering av IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Detaljerad information och en genomgång för migrering finns i [tekniska ingående om plattformen stöds från klassiska till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md)

<br>
Här är ett flödesschema för att identifiera den ordning som steg måste utföras under en migreringsprocessen

![Skärmbild som visar migreringsstegen](media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-plan-for-migration"></a>Steg 1: Planera för migrering
Här följer några metodtips som vi rekommenderar medan du utvärderar migrera IaaS-resurser från klassiska till Resource Manager:

* Läs igenom den [stöds inte funktioner och konfigurationer som stöds och](migration-classic-resource-manager-overview.md). Om du har virtuella datorer som använder icke-stödda konfigurationer eller funktioner, rekommenderar vi att du väntar configuration/funktionsstöd meddelas. Du kan också den passar dina behov ta bort funktionen eller flytta från den konfigurationen vill aktivera migrering.
* Om du har automatiserat skript som distribuerar din infrastruktur och dina program idag, försök att skapa en liknande inställningar med hjälp av dessa skript för migrering. Du kan också ställa in provet miljöer med hjälp av Azure portal.

> [!IMPORTANT]
> Programgatewayer stöds inte för migrering från classic till Resource Manager. Ta bort gatewayen innan du kör en Förberedelseåtgärden om du vill flytta nätverket för att migrera ett klassiskt virtuellt nätverk med en Programgateway. När du har slutfört migreringen återansluta gateway i Azure Resource Manager.
>
>ExpressRoute-gatewayer som ansluter till ExpressRoute-kretsar i en annan prenumeration som inte kan migreras automatiskt. I sådana fall kan ta bort ExpressRoute-gatewayen, migrera det virtuella nätverket och skapa gatewayen på nytt. Se [migrera ExpressRoute-kretsar och associerade virtuella nätverk från klassiskt till Resource Manager-distributionsmodellen](../../expressroute/expressroute-migration-classic-resource-manager.md) för mer information.

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Steg 2: Installera den senaste versionen av Azure PowerShell
Det finns två huvudsakliga alternativ för att installera Azure PowerShell: [PowerShell-galleriet](https://www.powershellgallery.com/profiles/azure-sdk/) eller [installationsprogram för webbplattform (WebPI)](http://aka.ms/webpi-azps). WebPI tar emot uppdateringar varje månad. PowerShell-galleriet tar emot uppdateringar kontinuerligt. Den här artikeln är baserad på Azure PowerShell version 2.1.0.

Installationsanvisningar finns i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>Steg 3: Kontrollera att du är administratör för prenumerationen i Azure-portalen
Om du vill utföra migreringen måste du måste läggas till som medadministratör för prenumerationen på den [Azure-portalen](https://portal.azure.com).

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. På navmenyn väljer **prenumeration**. Om du inte ser det, väljer **alla tjänster**.
3. Hitta lämpliga prenumerationspost titta sedan på den **Mina ROLLEN** fältet. För en medadministratör. värdet bör vara _kontoadministratören_.

Om du inte kan lägga till en medadministratör kontakta en administratör eller medadministratör för prenumerationen för att hämta själv lagts till.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Steg 4: Ange din prenumeration och registrera dig för migrering
Först, starta PowerShell-Kommandotolken. För migrering, måste du konfigurera din miljö för både klassiska och Resource Manager.

Logga in på ditt konto för Resource Manager-modellen.

```powershell
    Connect-AzureRmAccount
```

Hämta tillgängliga prenumerationer med hjälp av följande kommando:

```powershell
    Get-AzureRMSubscription | Sort Name | Select Name
```

Ange din Azure-prenumeration för den aktuella sessionen. Det här exemplet anges prenumeration standardnamnet **min Azure-prenumeration**. Ersätt prenumerationsnamn exempel med din egen.

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Registreringen är en enstaka steg, men du måste göra detta en gång innan du försöker migrera. Utan registrering visas följande felmeddelande:
>
> *BadRequest: Prenumerationen har inte registrerats för migrering.*

Registrera med resursprovidern migrering med hjälp av följande kommando:

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Vänta 5 minuter att slutföra registreringen. Du kan kontrollera status för godkännande med hjälp av följande kommando:

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Se till att RegistrationState `Registered` innan du fortsätter.

Nu logga in på ditt konto för den klassiska modellen.

```powershell
    Add-AzureAccount
```

Hämta tillgängliga prenumerationer med hjälp av följande kommando:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Ange din Azure-prenumeration för den aktuella sessionen. Det här exemplet anger standard-prenumeration till **min Azure-prenumeration**. Ersätt prenumerationsnamn exempel med din egen.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Steg 5: Kontrollera att du har tillräckligt med Azure Resource Manager-dator vCPUs i Azure-regionen för din aktuella distributionen eller virtuella nätverk
Du kan använda följande PowerShell-kommando för att kontrollera det aktuella antalet vCPUs som du har i Azure Resource Manager. Mer information om vCPU kvoter finns [gränser och Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager).

Det här exemplet kontrollerar tillgängligheten den **västra USA** region. Ersätt region Exempelnamn med din egen.

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>Steg 6: Kör kommandon för att migrera IaaS-resurser
* [Migrera virtuella datorer i en molntjänst (inte i det virtuella nätverket)](#step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migrera virtuella datorer i ett virtuellt nätverk](#step-61-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrera Lagringskontot](#step-62-migrate-a-storage-account)

> [!NOTE]
> Alla åtgärder som beskrivs här är idempotent. Om du har problem med än en funktion som inte stöds eller ett konfigurationsfel rekommenderar vi att du försöka med att förbereda, avbrytas eller genomföras igen. Plattformen försöker sedan igen.


### <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Steg 6.1: Alternativ 1 – migrera virtuella datorer i en tjänst i molnet (inte i ett virtuellt nätverk)
Med följande kommando för att hämta listan över molntjänster och välj sedan den molntjänst som du vill migrera. Om de virtuella datorerna i Molntjänsten är i ett virtuellt nätverk eller om de har webb-eller arbetsroller kommandot returnerar ett felmeddelande.

```powershell
    Get-AzureService | ft Servicename
```

Hämta distributionens namn för Molntjänsten. I det här exemplet tjänstnamnet är **Mina tjänsten**. Ersätt tjänstnamnet exempel med egna namn.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Förbered de virtuella datorerna i Molntjänsten för migrering. Du har två alternativ att välja mellan.

* **Alternativ 1. Migrera de virtuella datorerna till ett virtuellt nätverk skapas av plattform**

    Verifiera först om du kan migrera Molntjänsten med hjälp av följande kommandon:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Följande kommando visar alla varningar och fel som blockerar migrering. Om verifieringen lyckas, så du kan gå vidare till den **Förbered** steg:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Alternativ 2. Migrera till ett befintligt virtuellt nätverk i Resource Manager-distributionsmodellen**

    Det här exemplet anger resursgruppens namn till **myResourceGroup**, det virtuella nätverksnamnet till **myVirtualNetwork** och undernätsnamn till **mySubNet**. Ersätt namnen i exemplet med namnen på de egna resurser.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Verifiera först om du kan migrera det virtuella nätverket med hjälp av följande kommando:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Följande kommando visar alla varningar och fel som blockerar migrering. Om verifieringen lyckas, kan du fortsätta med följande förberedelsesteget:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

När Förberedelseåtgärden lyckas med något av alternativen ovan, fråga migreringstillståndet för de virtuella datorerna. Se till att de är i den `Prepared` tillstånd.

Det här exemplet anger namnet på virtuella datorn till **myVM**. Ersätt exempelnamnet med ditt eget namn på virtuell dator.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Kontrollera konfigurationen för de förberedda resurserna med hjälp av PowerShell eller Azure-portalen. Om du inte är klar för migrering och du vill gå tillbaka till ett tidigare tillstånd, använder du följande kommando:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Om den förberedda konfigurationen ser bra ut du gå vidare och genomför resurserna med hjälp av följande kommando:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Steg 6.1: Alternativ 2 – migrera virtuella datorer i ett virtuellt nätverk

Om du vill migrera virtuella datorer i ett virtuellt nätverk, kan du migrera det virtuella nätverket. De virtuella datorerna migrera automatiskt med det virtuella nätverket. Välj det virtuella nätverk som du vill migrera.
> [!NOTE]
> [Migrera klassiska virtuell dator](migrate-single-classic-to-resource-manager.md) genom att skapa en ny Resource Manager virtuell dator med hanterade diskar med hjälp av VHD-(OS och data) filerna för den virtuella datorn.
<br>

> [!NOTE]
> Det virtuella nätverksnamnet kan skilja sig från vad som visas i den nya portalen. Den nya Azure-portalen visar namn som `[vnet-name]` men det faktiska virtuella nätverksnamnet är av typen `Group [resource-group-name] [vnet-name]`. Innan du migrerar, sökning faktiska virtuella nätverksnamn med hjälp av kommandot `Get-AzureVnetSite | Select -Property Name` eller visa i den gamla Azure-portalen. 

Det här exemplet anges det virtuella nätverksnamnet **myVnet**. Ersätt det virtuella nätverksnamnet exempel med din egen.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Om det virtuella nätverket innehåller webb- eller arbetsroller eller virtuella datorer med konfigurationer som inte stöds, kan du få ett felmeddelande för verifiering.

Verifiera först om du kan migrera det virtuella nätverket med hjälp av följande kommando:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Följande kommando visar alla varningar och fel som blockerar migrering. Om verifieringen lyckas, kan du fortsätta med följande förberedelsesteget:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Kontrollera konfigurationen för förberedda virtuella datorer med hjälp av Azure PowerShell eller Azure-portalen. Om du inte är klar för migrering och du vill gå tillbaka till ett tidigare tillstånd, använder du följande kommando:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Om den förberedda konfigurationen ser bra ut du gå vidare och genomför resurserna med hjälp av följande kommando:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-62-migrate-a-storage-account"></a>Steg 6.2 Migrera ett lagringskonto
När du är klar migrering av virtuella datorer, rekommenderar vi du utföra följande kontroller av förutsättningar innan du migrerar storage-konton.

> [!NOTE]
> Om ditt lagringskonto har ingen associerad diskar eller VM-data, du kan hoppa över direkt till den **Validera Storage-konto och starta migreringen** avsnitt.

* **Nödvändiga kontroller om du har migrerat virtuella datorer eller ditt lagringskonto har diskresurser**
    * **Migrera den klassiska virtuella datorer vars diskar lagras i storage-konto**

        Följande kommando returnerar RoleName och DiskName egenskaper för alla klassiska Virtuella diskar på lagringskontot. RoleName är namnet på den virtuella datorn som en disk är ansluten. Om det här kommandot returnerar kontrollerar diskar sedan du att virtuella datorer till vilka diskarna är anslutna migreras innan du migrerar storage-konto.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * **Ta bort beräkningsfunktionen klassiska Virtuella diskar som lagras i storage-konto**

        Hitta beräkningsfunktionen klassiska Virtuella diskar i lagringen konto med hjälp av följande kommando:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Om ovanstående kommando returnerar diskar sedan ta bort dessa diskar med hjälp av följande kommando:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * **Ta bort VM-avbildningar lagras i storage-konto**

        Följande kommando returnerar alla VM-avbildningar med OS-disken som lagras i lagringskontot.
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
        Ta bort alla VM-avbildningar som returneras av ovan kommandon med hjälp av det här kommandot:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* **Validera Storage-kontot och starta migrering**

    Verifiera varje lagringskonto för migrering med hjälp av följande kommando. I det här exemplet är lagringskontonamnet **Mittlagringskonto**. Ersätt namnet på exemplet med namnet på ditt eget lagringskonto.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    Nästa steg är att förbereda storage-konto för migrering

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Kontrollera konfigurationen för förberedda storage-konto med hjälp av Azure PowerShell eller Azure-portalen. Om du inte är klar för migrering och du vill gå tillbaka till ett tidigare tillstånd, använder du följande kommando:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Om den förberedda konfigurationen ser bra ut du gå vidare och genomför resurserna med hjälp av följande kommando:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Nästa steg
* [Översikt över plattformar som stöds migrering av IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använda CLI för att migrera IaaS-resurser från klassiska till Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste frågorna om migrera IaaS-resurser från klassiska till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

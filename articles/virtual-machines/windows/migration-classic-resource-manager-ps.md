---
title: Migrera till Resource Manager med PowerShell | Microsoft Docs
description: Den här artikeln beskriver plattformsunderstödd migrering av IaaS-resurser som virtuella datorer (VM), virtuella nätverk (Vnet) och storage-konton från klassisk till Azure Resource Manager (ARM) med hjälp av Azure PowerShell-kommandon
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
ms.openlocfilehash: 5e905168ab2c2f10bcfadfc605fdcaa800e70332
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982015"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migrera IaaS-resurser från klassisk till Azure Resource Manager med hjälp av Azure PowerShell
De här stegen visar hur du använder Azure PowerShell-kommandon för att migrera infrastruktur som en tjänst (IaaS)-resurser från den klassiska distributionsmodellen Azure Resource Manager-distributionsmodellen.

Om du vill kan du också migrera resurser genom att använda den [Azure-kommandoradsgränssnittet (Azure CLI)](../linux/migration-classic-resource-manager-cli.md).

* Mer bakgrundsinformation om migreringsscenarier som stöds finns i [plattformsunderstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Detaljerad vägledning och en genomgång för migrering finns i [teknisk djupdykning i plattformsunderstödd migrering från klassisk till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md)

<br>
Här är ett flödesschema för att identifiera den ordning som stegen måste utföras en Migreringsprocess

![Skärmbild som visar migreringsstegen](media/migration-classic-resource-manager/migration-flow.png)

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="step-1-plan-for-migration"></a>Steg 1: Planera för migrering
Här följer några metodtips som vi rekommenderar medan du utvärderar migrera IaaS-resurser från klassisk till Resource Manager:

* Läs igenom den [stöds respektive inte stöds funktioner och konfigurationer](migration-classic-resource-manager-overview.md). Om du har virtuella datorer som använder konfigurationer som inte stöds eller funktioner, rekommenderar vi att du väntar configuration/funktionsstöd meddelas. Du kan också det passar dina behov, ta bort funktionen eller flytta från den konfigurationen för att aktivera migrering.
* Om du har automatiserat skript som distribuerar din infrastruktur och dina program idag, försök att skapa en liknande test-konfiguration med hjälp av dessa skript för migrering. Du kan också ställa in exempelmiljöer med hjälp av Azure portal.

> [!IMPORTANT]
> Programgatewayer stöds inte för migrering från klassisk till Resource Manager. Ta bort gatewayen innan du kör en Förbered-åtgärden för att flytta nätverket för att migrera ett klassiskt virtuellt nätverk med en Programgateway. När du har slutfört migreringen kan du återansluta gateway i Azure Resource Manager.
>
>ExpressRoute-gatewayer som ansluter till ExpressRoute-kretsar i en annan prenumeration migreras inte automatiskt. I sådana fall kan du ta bort ExpressRoute-gatewayen, migrera det virtuella nätverket och återskapa gatewayen. Se [migrera ExpressRoute circuits och tillhörande virtuella nätverk från klassiskt till Resource Manager-distributionsmodellen](../../expressroute/expressroute-migration-classic-resource-manager.md) för mer information.

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Steg 2: Installera den senaste versionen av Azure PowerShell
Det finns två huvudsakliga alternativ för att installera Azure PowerShell: [PowerShell-galleriet](https://www.powershellgallery.com/profiles/azure-sdk/) eller [Web Platform Installer (WebPI)](https://aka.ms/webpi-azps). Installationsprogram för webbplattform får månatliga uppdateringar. PowerShell-galleriet tar emot uppdateringar regelbundet. Den här artikeln är baserad på Azure PowerShell-version 2.1.0.

Installationsanvisningar finns i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>Steg 3: Se till att du är administratör för prenumerationen på Azure-portalen
För att genomföra migreringen, du måste läggas till som medadministratör för prenumerationen på den [Azure-portalen](https://portal.azure.com).

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. På navmenyn väljer **prenumeration**. Om du inte ser det, väljer **alla tjänster**.
3. Hitta en lämplig prenumeration-post och titta på den **min roll** fält. För en delad administratör. värdet bör vara _kontoadministratören_.

Om det inte går att lägga till en delad administratör, kontaktar du en tjänstadministratör eller delad administratör för prenumerationen för att få till dig själv.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Steg 4: Ställ in prenumerationen och registrera dig för migrering
Starta först en PowerShell-kommandotolk. För migrering, måste du konfigurera din miljö för både klassiska och Resource Manager.

Logga in på ditt konto för Resource Manager-modellen.

```powershell
    Connect-AzAccount
```

Hämta tillgängliga prenumerationer med hjälp av följande kommando:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Ange din Azure-prenumeration för den aktuella sessionen. Det här exemplet anges prenumerationsnamnet standard **min Azure-prenumeration**. Ersätt exempel prenumerationens namn med dina egna.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Registreringen är ett enstaka steg, men du måste göra det en gång innan du försöker migrera. Utan att registrera visas följande felmeddelande visas:
>
> *BadRequest: Prenumerationen har inte registrerats för migrering.*

Registrera med resursprovidern migrering med hjälp av följande kommando:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Vänta fem minuter att slutföra registreringen. Du kan kontrollera statusen för godkännandet med hjälp av följande kommando:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Se till att RegistrationState `Registered` innan du fortsätter.

Logga nu in på ditt konto för den klassiska modellen.

```powershell
    Add-AzureAccount
```

Hämta tillgängliga prenumerationer med hjälp av följande kommando:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Ange din Azure-prenumeration för den aktuella sessionen. Det här exemplet anger du standardprenumerationen till **min Azure-prenumeration**. Ersätt exempel prenumerationens namn med dina egna.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Steg 5: Kontrollera att du har tillräckligt med Azure Resource Manager-VM vcpu: er i Azure-regionen för din aktuella distributionen eller virtuella nätverk
Du kan använda följande PowerShell-kommando för att kontrollera det aktuella antalet virtuella processorer som du har i Azure Resource Manager. Läs mer om vCPU-kvoter i [gränser och Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager).

Det här exemplet kontrollerar tillgängligheten den **västra USA** region. Ersätt region Exempelnamn med dina egna.

```powershell
Get-AzVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>Steg 6: Kör kommandon för att migrera IaaS-resurser
* [Migrera virtuella datorer i en molntjänst (inte i virtuellt nätverk)](#step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migrera virtuella datorer i ett virtuellt nätverk](#step-61-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrera Storage-konto](#step-62-migrate-a-storage-account)

> [!NOTE]
> Alla åtgärder som beskrivs här är idempotenta. Om du har andra problem än en funktion som inte stöds eller ett konfigurationsfel rekommenderar vi att du gör om förbereda, avbryta eller utför-åtgärden. Plattformen försöker sedan igen.


### <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Steg 6.1: Alternativ 1 – migrera virtuella datorer i en molntjänst (inte i ett virtuellt nätverk)
Hämta listan över molntjänster med hjälp av följande kommando och välj sedan den molntjänst som du vill migrera. Om de virtuella datorerna i Molntjänsten är i ett virtuellt nätverk eller om de har web- eller worker-roller, returnerar kommandot ett felmeddelande.

```powershell
    Get-AzureService | ft Servicename
```

Hämta distributionsnamnet på för Molntjänsten. I det här exemplet är namnet på tjänsten **min tjänst**. Ersätt Exempelnamn för tjänsten med namnet på din egen tjänst.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Förbered de virtuella datorerna i Molntjänsten för migrering. Har du två alternativ att välja bland.

* **Alternativ 1. Migrera de virtuella datorerna till en plattform skapade virtuellt nätverk**

    Verifiera först om du kan migrera Molntjänsten med hjälp av följande kommandon:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Följande kommando visar alla varningar och fel som blockerar migrering. Om valideringen går bra så du kan gå vidare till den **Förbered** steg:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Alternativ 2. Migrera till ett befintligt virtuellt nätverk i Resource Manager-distributionsmodellen**

    Det här exemplet anger resursgruppens namn till **myResourceGroup**, det virtuella nätverksnamnet till **myVirtualNetwork** och namnet på undernätet till **mySubNet**. Ersätt namnen i det här exemplet med namnen på dina egna resurser.

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

Fråga efter migreringstillståndet för de virtuella datorerna när Förberedelseåtgärden lyckas med något av föregående alternativ. Se till att de är i den `Prepared` tillstånd.

Det här exemplet anges VM-namnet **myVM**. Ersätt exempelnamnet med ditt eget namn på virtuell dator.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Kontrollera konfigurationen för de förberedda resurserna med hjälp av PowerShell eller Azure-portalen. Om du inte är redo för migrering och du vill gå tillbaka till det gamla tillståndet, använder du följande kommando:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Om den förberedda konfigurationen ser bra ut du gå vidare och sparar resurserna med hjälp av följande kommando:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Steg 6.1: Alternativ 2 – migrera virtuella datorer i ett virtuellt nätverk

För att migrera virtuella datorer i ett virtuellt nätverk måste migrera du det virtuella nätverket. Virtuella datorer migreras automatiskt med det virtuella nätverket. Välj det virtuella nätverk som du vill migrera.
> [!NOTE]
> [Migrera en klassisk virtuell dator](migrate-single-classic-to-resource-manager.md) genom att skapa en ny Resource Manager virtuell dator med hanterade diskar med hjälp av VHD (operativsystem och data)-filerna på den virtuella datorn.
<br>

> [!NOTE]
> Det virtuella nätverksnamnet kan skilja sig från vad som anges i den nya portalen. Den nya Azure-portalen visar namnet som `[vnet-name]` men det faktiska virtuella nätverksnamnet är av typen `Group [resource-group-name] [vnet-name]`. Innan du migrerar, sökning faktiska virtuella nätverksnamn med hjälp av kommandot `Get-AzureVnetSite | Select -Property Name` eller visa den i den gamla Azure-portalen. 

Det här exemplet anges det virtuella nätverksnamnet **myVnet**. Ersätt det virtuella nätverksnamnet för exemplet med dina egna.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Om det virtuella nätverket innehåller webb- eller worker-roller eller virtuella datorer med konfigurationer som inte stöds, får du ett felmeddelande för verifiering.

Verifiera först om du kan migrera det virtuella nätverket med hjälp av följande kommando:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Följande kommando visar alla varningar och fel som blockerar migrering. Om verifieringen lyckas, kan du fortsätta med följande förberedelsesteget:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Kontrollera konfigurationen för förberedda virtuella datorer med hjälp av Azure PowerShell eller Azure-portalen. Om du inte är redo för migrering och du vill gå tillbaka till det gamla tillståndet, använder du följande kommando:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Om den förberedda konfigurationen ser bra ut du gå vidare och sparar resurserna med hjälp av följande kommando:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-62-migrate-a-storage-account"></a>Steg 6.2 Migrera ett lagringskonto
När du är klar migrerar de virtuella datorerna, rekommenderar vi du utföra följande kontroller av förutsättningar innan du migrerar storage-konton.

> [!NOTE]
> Om ditt lagringskonto har någon tillhörande diskar eller VM-data, du kan hoppa över direkt till den **Validera Storage-konto och starta migrering** avsnittet.

* **Nödvändiga kontroller om du har migrerat alla virtuella datorer eller ditt storage-konto har diskresurser**
    * **Migrera klassiska virtuella datorer vars diskarna lagras i lagringskontot**

        Följande kommando returnerar RoleName och DiskName egenskaper för alla klassiska Virtuella diskar i lagringskontot. RoleName är namnet på den virtuella datorn som en disk är ansluten. Om det här kommandot returnerar diskar ser du till att virtuella datorer som dessa diskar är anslutna migreras innan du migrerar storage-konto.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * **Ta bort icke anslutna klassiska Virtuella diskar som lagras i lagringskontot**

        Hitta icke anslutna diskar för klassiska VM i storage-konto med hjälp av följande kommando:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Om ovan kommandot returnerar diskar sedan tar du bort dessa diskar med hjälp av följande kommando:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * **Ta bort VM-avbildningar som lagras i lagringskontot**

        Följande kommando returnerar alla VM-avbildningar med OS-disken som lagras i lagringskontot.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         Följande kommando returnerar alla VM-avbildningar med datadiskar lagras i lagringskontot.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Ta bort alla VM-avbildningar som returneras av ovan kommandon med hjälp av det här kommandot:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* **Verifiera Storage-konto och börja migrering**

    Verifiera varje lagringskonto för migrering med hjälp av följande kommando. I det här exemplet är namnet på lagringskontot **myStorageAccount**. Ersätt exempelnamnet med namnet på ditt eget lagringskonto.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    Nästa steg är att förbereda storage-konto för migrering

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Kontrollera konfigurationen för förberedda storage-konto med hjälp av Azure PowerShell eller Azure-portalen. Om du inte är redo för migrering och du vill gå tillbaka till det gamla tillståndet, använder du följande kommando:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Om den förberedda konfigurationen ser bra ut du gå vidare och sparar resurserna med hjälp av följande kommando:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Nästa steg
* [Översikt över plattformsunderstödd migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd CLI för att migrera IaaS-resurser från klassisk till Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste frågorna om migrera IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

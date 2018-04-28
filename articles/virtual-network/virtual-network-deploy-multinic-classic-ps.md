---
title: Skapa en virtuell dator (klassisk) med flera nätverkskort – Azure PowerShell | Microsoft Docs
description: Lär dig hur du skapar en virtuell dator (klassisk) med flera nätverkskort med hjälp av PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 09c85a9badaad45816a9160d09cb3c4a726426e8
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>Skapa en virtuell dator (klassisk) med flera nätverkskort med hjälp av PowerShell

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Du kan skapa virtuella datorer (VM) i Azure och koppla flera nätverksgränssnitt (NIC) till var och en av dina virtuella datorer. Flera nätverkskort aktivera uppdelning av trafiktyper mellan nätverkskort. Till exempel kan ett nätverkskort kommunicera med Internet, medan en annan kommunicerar endast med interna resurser som inte är ansluten till Internet. Möjligheten att separera trafik över flera nätverkskort krävs för många virtuella nätverksenheter, till exempel leverans av program och optimering av WAN-lösningar.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Lär dig hur du utför dessa steg med hjälp av den [Resource Manager-distributionsmodellen](../virtual-machines/windows/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Följande steg använder en resursgrupp med namnet *IaaSStory* för webbservrar och en resursgrupp med namnet *IaaSStory BackEnd* för DB-servrar.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan skapa DB-servrar, måste du skapa den *IaaSStory* resursgrupp med alla nödvändiga resurser för det här scenariot. Följ instruktionerna som följer för att skapa dessa resurser. Skapa ett virtuellt nätverk genom att följa stegen i den [skapa ett virtuellt nätverk](virtual-networks-create-vnet-classic-netcfg-ps.md) artikel.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Skapa de virtuella datorerna serverdel
Backend-VMs beror på att skapa följande resurser:

* **Backend-undernät**. Databasservrar som ska ingå i ett separat undernät, segregera trafik. Skriptet nedan förväntar att det här undernätet måste finnas i ett vnet med namnet *WTestVnet*.
* **Storage-konto för datadiskar**. För bättre prestanda använder datadiskar på databasservrarna Solid-State-hårddisk (SSD)-teknik som kräver ett premiumlagringskonto. Kontrollera att den Azure-plats som du distribuerar för att stödja premium-lagring.
* **Tillgänglighetsuppsättning**. Alla databasservrar läggs till en enda tillgänglighet ange att se till att minst en av de virtuella datorerna är igång och körs under underhåll.

### <a name="step-1---start-your-script"></a>Steg 1 – starta skriptet
Du kan hämta den fullständiga PowerShell-skript som används [här](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Följ stegen nedan för att ändra skriptet fungerar i din miljö.

1. Ändra värdena för variablerna nedan baserat på en befintlig resursgrupp distribuerade ovan i [krav](#Prerequisites).

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Ändra värdena för variabler nedan baserat på de värden som du vill använda för backend-distribution.

    ```powershell
    $backendCSName         = "IaaSStory-Backend"
    $prmStorageAccountName = "iaasstoryprmstorage"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $diskSize              = 127
    $vmNamePrefix          = "DB"
    $dataDiskSuffix        = "datadisk"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Steg 2 – skapa nödvändiga resurser för din virtuella datorer
Du måste skapa en ny molntjänst och ett lagringskonto för datadiskar för alla virtuella datorer. Du måste också ange en bild och ett lokalt administratörskonto för de virtuella datorerna. För att skapa dessa resurser, gör du följande:

1. Skapa en ny molntjänst.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Skapa ett nytt premium-lagringskonto.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Ange det lagringskonto som skapade ovan som det aktuella lagringskontot för din prenumeration.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Välj en bild för den virtuella datorn.

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. Ange autentiseringsuppgifter för lokal administratör.

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>Steg 3 – skapa virtuella datorer
Du måste använda en loop skapa så många virtuella datorer som du vill och skapa de nödvändiga nätverkskort och virtuella datorer inom loopen. Utför följande steg för att skapa nätverkskort och virtuella datorer.

1. Starta en `for` slinga om du vill upprepa kommandona för att skapa en virtuell dator och två nätverkskort så många gånger som behövs, baserat på värdet för den `$numberOfVMs` variabeln.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Skapa en `VMConfig` -objekt som anger det bild, storlek och tillgänglighetsuppsättning för den virtuella datorn.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. Etablera den virtuella datorn som en Windows VM.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Ange standard nätverkskort och tilldela den en statisk IP-adress.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Lägga till ett andra nätverkskort för varje virtuell dator.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. Skapa till datadiskar för varje virtuell dator.

    ```powershell
    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk1Name `
    -LUN 0

    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk2Name `
    -LUN 1
    ```

7. Skapa varje virtuell dator och avsluta slingan.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>Steg 4 – kör skriptet
Nu när du har hämtat och ändra utifrån skriptet dina behov, runt skriptet för att skapa en backend-databas virtuella datorer med flera nätverkskort.

1. Spara skriptet och kör det från den **PowerShell** Kommandotolken eller **PowerShell ISE**. Inledande utdata visas enligt nedan.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Fyll i den information som behövs i fråga om autentiseringsuppgifterna och klicka på **OK**. Följande utdata returneras.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Steg 5 – konfigurera routning i operativsystemet för den virtuella datorn

Azure DHCP tilldelar en standard-gateway till det första (primära) nätverksgränssnittet kopplade till den virtuella datorn. Azure tilldelar inte en standardgateway till ytterligare (sekundära) nätverksgränssnitt som är kopplade till en virtuell dator. Du kan därför som standard inte kommunicera med resurser utanför det undernät som är ett sekundärt nätverksgränssnitt befinner sig i. Sekundära nätverksgränssnitt kan dock kommunicera med resurser utanför deras undernät. Om du vill konfigurera routning för sekundära nätverksgränssnitt, se [routning i ett operativsystem för virtuella datorer med flera nätverksgränssnitt](virtual-network-network-interface-vm.md).

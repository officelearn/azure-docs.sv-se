---
title: Skapa en virtuell dator (klassisk) med flera nätverkskort – Azure PowerShell | Microsoft Docs
description: Lär dig hur du skapar en virtuell dator (klassisk) med flera nätverkskort med hjälp av PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: af22bc43a06be74c7a4b6c869725a19fc87a0f3e
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058734"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>Skapa en virtuell dator (klassisk) med flera nätverkskort med hjälp av PowerShell

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Du kan skapa virtuella datorer i Azure och koppla flera nätverks gränssnitt (NIC) till var och en av dina virtuella datorer. Flera nätverkskort möjliggör separering av trafik typer över nätverkskort. Ett nätverkskort kan till exempel kommunicera med Internet, medan en annan bara kommunicerar med interna resurser som inte är anslutna till Internet. Möjligheten att separera nätverks trafik över flera nätverkskort krävs för många virtuella nätverks enheter, till exempel program leverans-och WAN-optimerings lösningar.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser:  [Resource Manager och klassisk](../resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Lär dig hur du utför dessa steg med hjälp av [distributions modellen för Resource Manager](../virtual-machines/windows/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

I följande steg används en resurs grupp med namnet *IaaSStory* för webb servrarna och en resurs grupp med namnet *IaaSStory-BackEnd* för DB-servrarna.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan skapa DB-servrarna måste du skapa resurs gruppen *IaaSStory* med alla nödvändiga resurser för det här scenariot. Slutför stegen nedan för att skapa dessa resurser. Skapa ett virtuellt nätverk genom att följa stegen i artikeln [skapa ett virtuellt nätverk](virtual-networks-create-vnet-classic-netcfg-ps.md) .

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Skapa backend-VM: ar
De virtuella server dels datorerna beror på skapandet av följande resurser:

* **Backend-undernät**. Databas servrarna kommer att ingå i ett separat undernät, för att åtskilja trafiken. Skriptet nedan förväntar sig att det här under nätet finns i ett virtuellt nätverk med namnet *WTestVnet*.
* **Lagrings konto för data diskar**. För bättre prestanda använder data diskarna på databas servrarna SSD-teknik (Solid State Drive), som kräver ett Premium Storage-konto. Kontrol lera att Azure-platsen som du distribuerar för att stödja Premium Storage.
* **Tillgänglighetsuppsättning**. Alla databas servrar läggs till i en enda tillgänglighets uppsättning för att säkerställa att minst en av de virtuella datorerna är igång under underhållet.

### <a name="step-1---start-your-script"></a>Steg 1 – Starta ditt skript
Du kan ladda ned det fullständiga PowerShell-skriptet som används [här](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Följ stegen nedan om du vill ändra skriptet så att det fungerar i din miljö.

1. Ändra värdena för variablerna nedan baserat på din befintliga resurs grupp som distribueras ovan i [krav](#prerequisites).

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Ändra värdena för variablerna nedan baserat på de värden som du vill använda för Server dels distributionen.

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

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Steg 2 – skapa nödvändiga resurser för dina virtuella datorer
Du måste skapa en ny moln tjänst och ett lagrings konto för data diskarna för alla virtuella datorer. Du måste också ange en avbildning och ett lokalt administratörs konto för de virtuella datorerna. Utför följande steg för att skapa dessa resurser:

1. Skapa en ny moln tjänst.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Skapa ett nytt Premium Storage-konto.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Ange det lagrings konto som skapades ovan som det aktuella lagrings kontot för din prenumeration.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Välj en avbildning för den virtuella datorn.

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. Ange autentiseringsuppgifterna för det lokala administratörs kontot.

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>Steg 3 – skapa virtuella datorer
Du måste använda en loop för att skapa så många virtuella datorer du vill och skapa nödvändiga nätverkskort och virtuella datorer i slingan. Utför följande steg för att skapa nätverkskorten och de virtuella datorerna.

1. Starta en `for` slinga för att upprepa kommandona för att skapa en virtuell dator och två nätverkskort så många gånger som det behövs, baserat på `$numberOfVMs` variabelns värde.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Skapa ett `VMConfig` objekt som anger avbildning, storlek och tillgänglighets uppsättning för den virtuella datorn.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. Etablera den virtuella datorn som en virtuell Windows-dator.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Ange standard NÄTVERKSKORTet och tilldela det en statisk IP-adress.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Lägg till ett andra nätverkskort för varje virtuell dator.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. Skapa till data diskar för varje virtuell dator.

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

7. Skapa varje virtuell dator och avsluta loopen.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>Steg 4 – kör skriptet
Nu när du laddade ned och ändrat skriptet baserat på dina behov, runt skriptet för att skapa backend-databasens virtuella datorer med flera nätverkskort.

1. Spara skriptet och kör det från **PowerShell** -Kommandotolken eller **PowerShell ISE**. Du kommer att se de första utdata som visas nedan.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Fyll i informationen som behövs i frågan om autentiseringsuppgifter och klicka på **OK**. Följande utdata returneras.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Steg 5 – konfigurera routning i den virtuella datorns operativ system

Azure DHCP tilldelar en standardgateway till det första nätverks gränssnittet (primär) som är kopplat till den virtuella datorn. Azure tilldelar inte en standardgateway till ytterligare (sekundära) nätverksgränssnitt som är kopplade till en virtuell dator. Du kan därför som standard inte kommunicera med resurser utanför det undernät som är ett sekundärt nätverksgränssnitt befinner sig i. Sekundära nätverksgränssnitt kan dock kommunicera med resurser utanför deras undernät. Information om hur du konfigurerar routning för sekundära nätverks gränssnitt finns i följande artiklar:

- [Konfigurera en virtuell Windows-dator för flera nätverkskort](../virtual-machines/windows/multiple-nics.md#configure-guest-os-for-multiple-nics
)

- [Konfigurera en virtuell Linux-dator för flera nätverkskort](../virtual-machines/linux/multiple-nics.md#configure-guest-os-for-multiple-nics
)

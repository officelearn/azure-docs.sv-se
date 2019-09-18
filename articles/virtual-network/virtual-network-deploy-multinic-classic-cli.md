---
title: Skapa en virtuell dator (klassisk) med flera nätverkskort – Azure Classic CLI | Microsoft Docs
description: Lär dig hur du skapar en virtuell dator (klassisk) med flera nätverkskort med hjälp av det klassiska kommando rads gränssnittet för Azure (CLI).
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: b436e41e-866c-439f-a7c7-7b4b041725ef
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2dc437b15f73866f76361da529690eac7a10af1a
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058747"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-classic-cli"></a>Skapa en virtuell dator (klassisk) med flera nätverkskort med hjälp av den klassiska Azure-CLI

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Du kan skapa virtuella datorer i Azure och koppla flera nätverks gränssnitt (NIC) till var och en av dina virtuella datorer. Flera nätverkskort möjliggör separering av trafik typer över nätverkskort. Ett nätverkskort kan till exempel kommunicera med Internet, medan en annan bara kommunicerar med interna resurser som inte är anslutna till Internet. Möjligheten att separera nätverks trafik över flera nätverkskort krävs för många virtuella nätverks enheter, till exempel program leverans-och WAN-optimerings lösningar.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser:  [Resource Manager och klassisk](../resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Lär dig hur du utför dessa steg med hjälp av [distributions modellen för Resource Manager](../virtual-machines/linux/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

I följande steg används en resurs grupp med namnet *IaaSStory* för webb servrarna och en resurs grupp med namnet *IaaSStory-BackEnd* för DB-servrarna.

## <a name="prerequisites"></a>Förutsättningar
Innan du kan skapa DB-servrarna måste du skapa resurs gruppen *IaaSStory* med alla nödvändiga resurser för det här scenariot. Slutför stegen nedan för att skapa dessa resurser. Skapa ett virtuellt nätverk genom att följa stegen i artikeln [skapa ett virtuellt nätverk](virtual-networks-create-vnet-classic-cli.md) .

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Distribuera backend-VM: ar
De virtuella server dels datorerna beror på skapandet av följande resurser:

* **Lagrings konto för data diskar**. För bättre prestanda använder data diskarna på databas servrarna SSD-teknik (Solid State Drive), som kräver ett Premium Storage-konto. Kontrol lera att Azure-platsen som du distribuerar för att stödja Premium Storage.
* **Nätverkskort**. Varje virtuell dator kommer att ha två nätverkskort, ett för databas åtkomst och ett för hantering.
* **Tillgänglighetsuppsättning**. Alla databas servrar läggs till i en enda tillgänglighets uppsättning för att säkerställa att minst en av de virtuella datorerna är igång under underhållet.

### <a name="step-1---start-your-script"></a>Steg 1 – Starta ditt skript
Du kan ladda ned det fullständiga bash-skriptet som används [här](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Utför följande steg för att ändra skriptet så att det fungerar i din miljö:

1. Ändra värdena för variablerna nedan baserat på din befintliga resurs grupp som distribueras ovan i [krav](#prerequisites).

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. Ändra värdena för variablerna nedan baserat på de värden som du vill använda för Server dels distributionen.

    ```azurecli
    backendCSName="IaaSStory-Backend"
    prmStorageAccountName="iaasstoryprmstorage"
    image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
    avSetName="ASDB"
    vmSize="Standard_DS3"
    diskSize=127
    vmNamePrefix="DB"
    osDiskName="osdiskdb"
    dataDiskPrefix="db"
    dataDiskName="datadisk"
    ipAddressPrefix="192.168.2."
    username='adminuser'
    password='adminP@ssw0rd'
    numberOfVMs=2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Steg 2 – skapa nödvändiga resurser för dina virtuella datorer
1. Skapa en ny moln tjänst för alla virtuella server delar. Observera användningen av `$backendCSName` variabeln för resurs gruppens namn och `$location` för Azure-regionen.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Skapa ett Premium Storage-konto för operativ system och data diskar som ska användas av dina virtuella datorer.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>Steg 3 – skapa virtuella datorer med flera nätverkskort
1. Starta en slinga för att skapa flera virtuella datorer baserat på `numberOfVMs` variablerna.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Ange namn och IP-adress för de båda nätverkskorten för varje virtuell dator.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. Skapa den virtuella datorn. Observera användningen av `--nic-config` parametern, som innehåller en lista över alla nätverkskort med namn, undernät och IP-adress.

    ```azurecli
    azure vm create $backendCSName $image $username $password \
        --connect $backendCSName \
        --vm-name $vmNamePrefix$suffixNumber \
        --vm-size $vmSize \
        --availability-set $avSetName \
        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
        --virtual-network-name $vnetName \
        --subnet-names $backendSubnetName \
        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::
    ```

4. Skapa två data diskar för varje virtuell dator.

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>Steg 4 – kör skriptet
Nu när du laddade ned och ändrat skriptet baserat på dina behov kan du köra skriptet för att skapa server dels databasens virtuella datorer med flera nätverkskort.

1. Spara skriptet och kör det från din **bash** -Terminal. Du kommer att se de första utdata som visas nedan.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Efter några minuter kommer körningen att avslutas och resten av utdata visas som visas nedan.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Steg 5 – konfigurera routning i den virtuella datorns operativ system

Azure DHCP tilldelar en standardgateway till det första nätverks gränssnittet (primär) som är kopplat till den virtuella datorn. Azure tilldelar inte en standardgateway till ytterligare (sekundära) nätverksgränssnitt som är kopplade till en virtuell dator. Du kan därför som standard inte kommunicera med resurser utanför det undernät som är ett sekundärt nätverksgränssnitt befinner sig i. Sekundära nätverksgränssnitt kan dock kommunicera med resurser utanför deras undernät. Om du vill konfigurera routning för sekundära nätverks gränssnitt, se [routning i ett operativ system för virtuella datorer med flera nätverks gränssnitt](virtual-network-network-interface-vm.md).

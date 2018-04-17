---
title: Skapa en virtuell dator (klassisk) med flera nätverkskort – Azure CLI 1.0 | Microsoft Docs
description: Lär dig hur du skapar en virtuell dator (klassisk) med flera nätverkskort som använder Azure-kommandoradsgränssnittet (CLI) 1.0.
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
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
ms.openlocfilehash: d2631e531368df4f00d7ffd8fef585038658797b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-cli-10"></a>Skapa en virtuell dator (klassisk) med flera nätverkskort som använder Azure CLI 1.0

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Du kan skapa virtuella datorer (VM) i Azure och koppla flera nätverksgränssnitt (NIC) till var och en av dina virtuella datorer. Flera nätverkskort aktivera uppdelning av trafiktyper mellan nätverkskort. Till exempel kan ett nätverkskort kommunicera med Internet, medan en annan kommunicerar endast med interna resurser som inte är ansluten till Internet. Möjligheten att separera trafik över flera nätverkskort krävs för många virtuella nätverksenheter, till exempel leverans av program och optimering av WAN-lösningar.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Lär dig hur du utför dessa steg med hjälp av den [Resource Manager-distributionsmodellen](../virtual-machines/linux/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Följande steg använder en resursgrupp med namnet *IaaSStory* för webbservrar och en resursgrupp med namnet *IaaSStory BackEnd* för DB-servrar.

## <a name="prerequisites"></a>Förutsättningar
Innan du kan skapa DB-servrar, måste du skapa den *IaaSStory* resursgrupp med alla nödvändiga resurser för det här scenariot. Följ instruktionerna som följer för att skapa dessa resurser. Skapa ett virtuellt nätverk genom att följa stegen i den [skapa ett virtuellt nätverk](virtual-networks-create-vnet-classic-cli.md) artikel.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Distribuera de virtuella datorerna serverdel
Backend-VMs beror på att skapa följande resurser:

* **Storage-konto för datadiskar**. För bättre prestanda använder datadiskar på databasservrarna Solid-State-hårddisk (SSD)-teknik som kräver ett premiumlagringskonto. Kontrollera att den Azure-plats som du distribuerar för att stödja premium-lagring.
* **Nätverkskort**. Varje virtuell dator har två nätverkskort, ett för åtkomst till databasen, och en för hantering.
* **Tillgänglighetsuppsättning**. Alla databasservrar läggs till en enda tillgänglighet ange att se till att minst en av de virtuella datorerna är igång och körs under underhåll.

### <a name="step-1---start-your-script"></a>Steg 1 – starta skriptet
Du kan hämta fullständig bash-skript används [här](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Utför följande steg om du vill ändra skriptet fungerar i din miljö:

1. Ändra värdena för variablerna nedan baserat på en befintlig resursgrupp distribuerade ovan i [krav](#Prerequisites).

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. Ändra värdena för variabler nedan baserat på de värden som du vill använda för backend-distribution.

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

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Steg 2 – skapa nödvändiga resurser för din virtuella datorer
1. Skapa en ny molntjänst för alla virtuella datorer i serverdelen. Observera användningen av den `$backendCSName` variabel för resursgruppens namn och `$location` för Azure-regionen.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Skapa ett premiumlagringskonto för Operativsystemet och datadiskarna som ska användas av dina virtuella datorer.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>Steg 3 – skapa virtuella datorer med flera nätverkskort
1. Starta en loop om du vill skapa flera virtuella datorer, baserat på den `numberOfVMs` variabler.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Ange namn och IP-adressen för var och en av de två nätverkskorten för varje virtuell dator.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. Skapa den virtuella datorn. Observera användningen av den `--nic-config` parametern, som innehåller en lista över alla nätverkskort med namnet, undernät och IP-adress.

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

4. Skapa två hårddiskar för varje virtuell dator.

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
Nu när du har hämtat och ändra skriptet baserat på dina behov, kör du skriptet för att skapa serverdelen databasen virtuella datorer med flera nätverkskort.

1. Spara skriptet och kör det från din **Bash** terminal. Inledande utdata visas enligt nedan.

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

2. Körningen avslutas efter några minuter och du kommer att se resten av utdata som visas nedan.

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

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Steg 5 – konfigurera routning i operativsystemet för den virtuella datorn

Azure DHCP tilldelar en standard-gateway till det första (primära) nätverksgränssnittet kopplade till den virtuella datorn. Azure tilldelar inte en standardgateway till ytterligare (sekundära) nätverksgränssnitt som är kopplade till en virtuell dator. Du kan därför som standard inte kommunicera med resurser utanför det undernät som är ett sekundärt nätverksgränssnitt befinner sig i. Sekundära nätverksgränssnitt kan dock kommunicera med resurser utanför deras undernät. Om du vill konfigurera routning för sekundära nätverksgränssnitt, se [routning i ett operativsystem för virtuella datorer med flera nätverksgränssnitt](virtual-network-network-interface-vm.md).

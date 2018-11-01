---
title: Skapa en intern lastbalanserare – klassisk Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en intern lastbalanserare med Azure CLI i den klassiska distributionsmodellen
services: load-balancer
documentationcenter: na
author: genlin
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c7659937599d778d9dc945254fcfff52840622dc
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50415074"
---
# <a name="get-started-creating-an-internal-load-balancer-using-the-azure-classic-cli"></a>Kom igång med att skapa en intern lastbalanserare med klassisk Azure CLI

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Molntjänster](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md).  Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Lär dig hur du [utför dessa steg med hjälp av Resource Manager-modellen](load-balancer-get-started-ilb-arm-cli.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Så här skapar du en intern lastbalanseringsuppsättning för virtuella datorer

Följ dessa steg för att skapa en intern lastbalanseringsuppsättning och de servrar som ska skicka sin trafik till den:

1. Skapa en instans för intern belastningsutjämning som ska vara slutpunkten för inkommande trafik som ska belastningsutjämnas mellan servrarna i en belastningsutjämnad uppsättning.
2. Lägg till slutpunkter som motsvarar de virtuella datorerna som kan ta emot den inkommande trafiken.
3. Konfigurera servrarna så att de skickar trafiken till den virtuella IP-adressen för instansen för intern belastningsutjämning.

## <a name="step-by-step-creating-an-internal-load-balancer-using-classic-cli"></a>Stegvisa anvisningar för att skapa en intern lastbalanserare med klassisk CLI

Den här guiden beskriver hur du skapar en intern lastbalanserare baserat på scenariot ovan.

1. Om du aldrig har använt klassisk CLI, se [Installera och konfigurera Azure CLI](../cli-install-nodejs.md) och följ instruktionerna upp till den punkt där du väljer ditt Azure-konto och -prenumeration.
2. Kör kommandot **azure config mode** för att växla till klassiskt läge, som du ser nedan.

    ```azurecli
    azure config mode asm
    ```

    Förväntad utdata:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Skapa slutpunkt och lastbalanseringsuppsättning

I det här scenariot förutsätter vi att de virtuella datorerna ”DB1” och ”DB2” i en molntjänst med namnet ”mytestcloud” redan finns. Båda de virtuella datorerna använder ett virtuellt nätverk med namnet ”testvnet” med undernätet ”subnet-1”.

I den här guiden skapar vi en intern lastbalanseringsuppsättning som använder port 1433 som privat port och 1433 som lokal port.

Det här är ett vanligt scenario där du har virtuella SQL-datorer på backend-servern som använder en intern lastbalanserare för att säkerställa att databasservrarna inte exponeras direkt genom användningen av en offentlig IP-adress.

### <a name="step-1"></a>Steg 1

Skapa en intern lastbalanseringsuppsättning med hjälp av `azure network service internal-load-balancer add`.

```azurecli
azure service internal-load-balancer add --serviceName mytestcloud --internalLBName ilbset --subnet-name subnet-1 --static-virtualnetwork-ipaddress 192.168.2.7
```

Mer information finns i `azure service internal-load-balancer --help`.

Du kan kontrollera egenskaperna för den interna lastbalanseraren med hjälp av kommandot `azure service internal-load-balancer list`*molntjänstens namn*.

Här är ett exempel på utdata som kan returneras:

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


### <a name="step-2"></a>Steg 2

Du konfigurerar den interna lastbalanseringsuppsättningen när du lägger till den första slutpunkten. Du associerar slutpunkten, den virtuella datorn och avsökningsporten till den interna lastbalanseringsuppsättningen i det här steget.

```azurecli
azure vm endpoint create db1 1433 --local-port 1433 --protocol tcp --probe-port 1433 --probe-protocol tcp --probe-interval 300 --probe-timeout 600 --internal-load-balancer-name ilbset
```

### <a name="step-3"></a>Steg 3

Kontrollera lastbalanserarens konfiguration med hjälp av `azure vm show`*namn på virtuell dator*

```azurecli
azure vm show DB1
```

Utdata är följande:

    azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Skapa en fjärrskrivbordsslutpunkt för en virtuell dator

Du kan skapa en fjärrskrivbordsslutpunkt för att vidarebefordra nätverkstrafik från en offentlig port till en lokal port för en specifik virtuell dator med hjälp av `azure vm endpoint create`.

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>Ta bort en virtuell dator från lastbalanseraren

Du kan ta bort en virtuell dator från en intern lastbalanseringsuppsättning genom att ta bort den associerade slutpunkten. När slutpunkten har tagits bort tillhör inte den virtuella datorn lastbalanseringsuppsättningen längre.

Om du följer exemplet ovan kan du ta bort slutpunkten som skapats för den virtuella datorn ”DB1” från den interna lastbalanseringsuppsättningen ”ilbset” med hjälp av kommandot `azure vm endpoint delete`.

```azurecli
azure vm endpoint delete DB1 tcp-1433-1433
```

Mer information finns i `azure vm endpoint --help`.

## <a name="next-steps"></a>Nästa steg

[Konfigurera ett distributionsläge för lastbalanserare med hjälp av käll-IP-tilldelning](load-balancer-distribution-mode.md)

[Konfigurera timeout-inställningar för inaktiv TCP för en lastbalanserare](load-balancer-tcp-idle-timeout.md)

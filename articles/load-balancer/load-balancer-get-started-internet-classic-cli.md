---
title: Skapa en Internetriktad lastbalanserare – klassisk Azure CLI | Microsoft Docs
description: Läs hur du skapar en Internetriktad lastbalanserare i den klassiska distributionsmodellen med hjälp av den klassiska Azure CLI
services: load-balancer
documentationcenter: na
author: genlin
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: a0640e3d0b7ccc11ee5603a9b1f21daf53318266
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740509"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-cli"></a>Kom igång med att skapa en Internetriktad lastbalanserare (klassisk) på den klassiska Azure CLI

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Klassisk Azure CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Innan du börjar arbeta med Azure-resurser är det viktigt att du vet att Azure för närvarande har två distributionsmodeller: Azure Resource Manager och klassisk. Se till att du förstår [distributionsmodeller och verktyg](../azure-classic-rm.md) innan du börjar arbeta med Azure-resurser. Du kan granska dokumentationen för olika verktyg genom att klicka på flikarna överst i den här artikeln. Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också läsa artikeln om [hur du skapar en Internetuppkopplad lastbalanserare med hjälp av Azure Resource Manager](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [requires-classic-cli](../../includes/contains-classic-cli-content.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="create-an-internet-facing-load-balancer-using-cli"></a>Skapa en Internetuppkopplad lastbalanserare med CLI

Den här guiden beskriver hur du skapar en Internetuppkopplad lastbalanserare baserat på scenariot ovan.

1. Om du aldrig har använt klassisk Azure CLI, se [Installera och konfigurera klassisk Azure CLI](../cli-install-nodejs.md) och följ instruktionerna upp till den punkt där du väljer ditt Azure-konto och prenumeration.
2. Kör kommandot **azure config mode** för att växla till klassiskt läge, som du ser nedan.

    ```azurecli
    azure config mode asm
    ```

    Förväntad utdata:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Skapa slutpunkt och lastbalanseringsuppsättning

I det här scenariot förutsätter vi att de virtuella datorerna ”web1” och ”web2” har skapats.
I den här guiden skapar vi en lastbalanseringsuppsättning som använder port 80 som offentlig port och port 80 som lokal port. En avsökningsport konfigureras också på port 80 och lastbalanseringsuppsättningen tilldelas namnet ”lbset”.

### <a name="step-1"></a>Steg 1

Skapa den första slutpunkten och lastbalanseringsuppsättningen med hjälp av `azure network vm endpoint create` för den virtuella datorn ”web1”.

```azurecli
azure vm endpoint create web1 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-2"></a>Steg 2

Lägg till en andra virtuell dator, ”web2”, i lastbalanseringsuppsättningen.

```azurecli
azure vm endpoint create web2 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-3"></a>Steg 3

Kontrollera lastbalanserarens konfiguration med hjälp av `azure vm show` .

```azurecli
azure vm show web1
```

Följande utdata returneras:

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Skapa en fjärrskrivbordsslutpunkt för en virtuell dator

Du kan skapa en fjärrskrivbordsslutpunkt för att vidarebefordra nätverkstrafik från en offentlig port till en lokal port för en specifik virtuell dator med hjälp av `azure vm endpoint create`.

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>Ta bort en virtuell dator från lastbalanseraren

Du måste ta bort slutpunkten som är kopplad till lastbalanseringsuppsättningen från den virtuella datorn. När slutpunkten har tagits bort tillhör inte den virtuella datorn lastbalanseringsuppsättningen längre.

I ovanstående exempel kan du ta bort slutpunkten som skapades för den virtuella datorn ”web1” från lastbalanseraren ”lbset” med hjälp av kommandot `azure vm endpoint delete`.

```azurecli
azure vm endpoint delete web1 tcp-80-80
```

> [!NOTE]
> Du kan utforska fler alternativ för hantering av slutpunkter med hjälp av kommandot `azure vm endpoint --help`

## <a name="next-steps"></a>Nästa steg

[Komma igång med att konfigurera en intern lastbalanserare](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurera ett distributionsläge för lastbalanserare](load-balancer-distribution-mode.md)

[Konfigurera timeout-inställningar för inaktiv TCP för en lastbalanserare](load-balancer-tcp-idle-timeout.md)

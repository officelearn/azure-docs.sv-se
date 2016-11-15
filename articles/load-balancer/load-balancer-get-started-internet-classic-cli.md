---
title: "Komma igång med att skapa en Internetuppkopplad belastningsutjämnare i den klassiska distributionsmodellen med hjälp av CLI | Microsoft Docs"
description: "Lär dig hur du skapar en Internetuppkopplad belastningsutjämnare i den klassiska distributionsmodellen med hjälp av Azure CLI"
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: e433a824-4a8a-44d2-8765-a74f52d4e584
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e8a346c1b2d430eceb4aa1b8bc94fbbe89394556


---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-cli"></a>Komma igång med att skapa en Internetuppkopplad belastningsutjämnare (klassisk) i Azure CLI
[!INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också läsa artikeln om [hur du skapar en Internetuppkopplad belastningsutjämnare med hjälp av Azure Resource Manager](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="step-by-step-creating-an-internet-facing-load-balancer-using-cli"></a>Stegvisa anvisningar som beskriver hur du skapar en Internetuppkopplad belastningsutjämnare med CLI
Den här guiden beskriver hur du skapar en Internetuppkopplad belastningsutjämnare baserat på scenariot ovan.

1. Om du aldrig har använt Azure CLI, se [installera och konfigurera Azure CLI](../xplat-cli-install.md) och följ instruktionerna upp till den punkt där du väljer Azure-konto och prenumeration.
2. Kör kommandot **azure config mode** för att växla till klassiskt läge, som du ser nedan.
   
        azure config mode asm
   
    Förväntad utdata:
   
        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Skapa slutpunkt och belastningsutjämningsuppsättning
I det här scenariot förutsätter vi att de virtuella datorerna ”web1” och ”web2” har skapats.
I den här guiden skapar vi en belastningsutjämningsuppsättning som använder port 80 som offentlig port och port 80 som lokal port. En avsökningsport konfigureras också på port 80 och belastningsutjämningsuppsättningen tilldelas namnet ”lbset”.

### <a name="step-1"></a>Steg 1
Skapa den första slutpunkten och belastningsutjämningsuppsättningen med hjälp av `azure network vm endpoint create` för den virtuella datorn ”web1”.

    azure vm endpoint create web1 80 -k 80 -o tcp -t 80 -b lbset

Parametrar som används:

**-k** – lokal VM-port<br>
**-o** – protokoll<BR>
**-t** – avsökningsport<BR>
**-b** – belastningsutjämnarens namn<BR>

## <a name="step-2"></a>Steg 2
Lägg till en andra virtuell dator, ”web2”, i belastningsutjämningsuppsättningen.

    azure vm endpoint create web2 80 -k 80 -o tcp -t 80 -b lbset

## <a name="step-3"></a>Steg 3
Kontrollera belastningsutjämnarens konfiguration med hjälp av `azure vm show` .

    azure vm show web1

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

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>Ta bort en virtuell dator från belastningsutjämnaren
Du måste ta bort slutpunkten som är kopplad till belastningsutjämningsuppsättningen från den virtuella datorn. När slutpunkten har tagits bort tillhör inte den virtuella datorn belastningsutjämningsuppsättningen längre.

 I ovanstående exempel kan du ta bort slutpunkten som skapades för den virtuella datorn ”web1” från belastningsutjämnaren ”lbset” med hjälp av kommandot `azure vm endpoint delete`.

    azure vm endpoint delete web1 tcp-80-80


> [!NOTE]
> Du kan utforska fler alternativ för hantering av slutpunkter med hjälp av kommandot `azure vm endpoint --help`
> 
> 

## <a name="next-steps"></a>Nästa steg
[Komma igång med att konfigurera en intern belastningsutjämnare](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurera ett distributionsläge för belastningsutjämnare](load-balancer-distribution-mode.md)

[Konfigurera timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md)




<!--HONumber=Nov16_HO2-->



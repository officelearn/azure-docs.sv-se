---
title: "Skapa en intern belastningsutjämnare med hjälp av Azure CLI i den klassiska distributionsmodellen | Microsoft Docs"
description: "Lär dig hur du skapar en intern belastningsutjämnare med hjälp av Azure CLI i den klassiska distributionsmodellen"
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: becbbbde-a118-4269-9444-d3153f00bf34
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4364d3bcdffd278bef35b224a8e22062814ca490


---
# <a name="get-started-creating-an-internal-load-balancer-classic-using-the-azure-cli"></a>Komma igång med att skapa en intern belastningsutjämnare (klassisk) med hjälp av Azure CLI
[!INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]

Lär dig hur du [utför dessa steg med hjälp av Resource Manager-modellen](load-balancer-get-started-ilb-arm-cli.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Så här skapar du en intern belastningsutjämningsuppsättning för virtuella datorer
Följ dessa steg för att skapa en intern belastningsutjämningsuppsättning och de servrar som ska skicka sin trafik till den:

1. Skapa en instans för intern belastningsutjämning som ska vara slutpunkten för inkommande trafik som ska belastningsutjämnas mellan servrarna i en belastningsutjämnad uppsättning.
2. Lägg till slutpunkter som motsvarar de virtuella datorerna som ska ta emot den inkommande trafiken.
3. Konfigurera servrarna som ska skicka trafiken som ska belastningsutjämnas så att de skickar trafiken till den virtuella IP-adressen för instansen för intern belastningsutjämning.

## <a name="step-by-step-creating-an-internal-load-balancer-using-cli"></a>Stegvisa anvisningar som beskriver hur du skapar en intern belastningsutjämnare med hjälp av CLI
Den här guiden beskriver hur du skapar en intern belastningsutjämnare baserat på scenariot ovan.

1. Om du aldrig har använt Azure CLI, se [installera och konfigurera Azure CLI](../xplat-cli-install.md) och följ instruktionerna upp till den punkt där du väljer Azure-konto och prenumeration.
2. Kör kommandot **azure config mode** för att växla till klassiskt läge, som du ser nedan.
   
        azure config mode asm
   
    Förväntad utdata:
   
        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Skapa slutpunkt och belastningsutjämningsuppsättning
I det här scenariot förutsätter vi att de virtuella datorerna ”DB1” och ”DB2” i en molntjänst med namnet ”mytestcloud” redan finns. Båda de virtuella datorerna använder ett virtuellt nätverk med namnet ”testvnet” med undernätet ”subnet-1”.

I den här guiden skapar vi en intern belastningsutjämningsuppsättning som använder port 1433 som privat port och 1433 som lokal port.

Det här är ett vanligt scenario där du har virtuella SQL-datorer på backend-servern som använder en intern belastningsutjämnare för att säkerställa att databasservrarna inte exponeras direkt genom användningen av en offentlig IP-adress.

### <a name="step-1"></a>Steg 1
Skapa en intern belastningsutjämningsuppsättning med hjälp av `azure network service internal-load-balancer add`.

     azure service internal-load-balancer add -r mytestcloud -n ilbset -t subnet-1 -a 192.168.2.7

Parametrar som används:

**-r** – molntjänstens namn<BR>
**-n** – namnet på den interna belastningsutjämnaren<BR>
**-t** –undernätets namn (samma undernät som för de virtuella datorer som du ska lägga till i den interna belastningsutjämnaren)<BR>
**-a** – (valfritt) lägg till en statisk privat IP-adress<BR>

Mer information finns i `azure service internal-load-balancer --help`.

Du kan kontrollera egenskaperna för den interna belastningsutjämnaren med hjälp av kommandot `azure service internal-load-balancer list` *molntjänstens namn*.

Här är ett exempel på utdata som kan returneras:

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


## <a name="step-2"></a>Steg 2
Du konfigurerar den interna belastningsutjämningsuppsättningen när du lägger till den första slutpunkten. Du associerar slutpunkten, den virtuella datorn och avsökningsporten till den intern belastningsutjämningsuppsättningen i det här steget.

    azure vm endpoint create db1 1433 -k 1433 tcp -t 1433 -r tcp -e 300 -f 600 -i ilbset

Parametrar som används:

**-k** – lokal VM-port<BR>
**-t** – avsökningsport<BR>
**-r** – avsökningsprotokoll<BR>
**-e** –avsökningsintervall i sekunder<BR>
**-f** – timeout-intervall i sekunder <BR>
**-i** – namnet på den interna belastningsutjämnaren <BR>

## <a name="step-3"></a>Steg 3
Kontrollera belastningsutjämnarens konfiguration med hjälp av `azure vm show` *namn på virtuell dator*

    azure vm show DB1

Följande utdata returneras:

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

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>Ta bort en virtuell dator från belastningsutjämnaren
Du kan ta bort en virtuell dator från en intern belastningsutjämningsuppsättning genom att ta bort den associerade slutpunkten. När slutpunkten har tagits bort tillhör inte den virtuella datorn belastningsutjämningsuppsättningen längre.

 Om du följer exemplet ovan kan du ta bort slutpunkten som skapats för den virtuella datorn ”DB1” från den intern belastningsutjämningsuppsättningen ”ilbset” med hjälp av kommandot `azure vm endpoint delete`.

    azure vm endpoint delete DB1 tcp-1433-1433


Mer information finns i `azure vm endpoint --help`.

## <a name="next-steps"></a>Nästa steg
[Konfigurera ett distributionsläge för belastningsutjämnare med hjälp av käll-IP-tilldelning](load-balancer-distribution-mode.md)

[Konfigurera timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md)




<!--HONumber=Nov16_HO2-->



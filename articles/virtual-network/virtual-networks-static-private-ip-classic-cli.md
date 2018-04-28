---
title: Konfigurera privata IP-adresser för virtuella datorer (klassisk) - Azure CLI 1.0 | Microsoft Docs
description: Lär dig hur du konfigurerar den privata IP-adresser för virtuella datorer (klassisk) med hjälp av Azure-kommandoradsgränssnittet (CLI) 1.0.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: 17386acf-c708-4103-9b22-ff9bf04b778d
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a18877167d04fdb039070d5315390a846925fd29
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-cli-10"></a>Konfigurera privat IP-adresser för en virtuell dator (klassisk) som använder Azure CLI 1.0

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också [hantera en statisk privat IP-adress i Resource Manager-distributionsmodellen](virtual-networks-static-private-ip-arm-cli.md).

Exemplet Azure CLI-kommandona nedan förväntar sig en enkel miljö som redan har skapats. Om du vill köra kommandon som de visas i det här dokumentet, först skapa testmiljön som beskrivs i [skapa ett vnet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Så här anger du en statisk privat IP-adress när du skapar en virtuell dator
Skapa en ny virtuell dator med namnet *DNS01* i en ny molntjänst med namnet *TestService* baserat på scenariot ovan, gör du följande:

1. Om du aldrig har använt Azure CLI, se [installera och konfigurera Azure CLI](../cli-install-nodejs.md) och följ instruktionerna upp till den punkt där du väljer Azure-konto och prenumeration.
2. Kör den **azure-tjänsten skapar** kommando för att skapa Molntjänsten.
   
        azure service create TestService --location uscentral
   
    Förväntad utdata:
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. Kör den **azure skapa vm** kommando för att skapa den virtuella datorn. Lägg märke till värdet för en statisk privat IP-adress. Listan som visas efter utdatan beskriver de parametrar som används.
   
        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd
   
    Förväntad utdata:
   
        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK
   
   * **-l (eller --location)**. Azure-region där den virtuella datorn kommer att skapas. I vårt scenario, *centralus*.
   * **-n (eller--vm-namn)**. Namnet på den virtuella datorn skapas.
   * **-w (eller--virtuella nätverksnamnet)**. Namnet på VNet där den virtuella datorn kommer att skapas. 
   * **-S (eller--statisk ip)**. Statisk privat IP-adress för den virtuella datorn.
   * **TestService**. Namnet på Molntjänsten där den virtuella datorn kommer att skapas.
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**. Bild som används för att skapa den virtuella datorn.
   * **adminuser**. Lokal administratör för Windows-VM.
   * **AdminP@ssw0rd**. Lokala administratörslösenordet för Windows-VM.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hur du hämtar statisk privat IP-adressinformation för en virtuell dator
Kör följande kommando för Azure CLI för att visa statisk privat IP-adressinformation för den virtuella datorn skapas med skriptet ovan, och Lägg märke till värdet för *nätverk StaticIP*:

    azure vm static-ip show DNS01

Förväntad utdata:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Ta bort en statisk privat IP-adress från en virtuell dator
Att ta bort statisk privat IP-adress till den virtuella datorn i skriptet ovan, kör följande kommando för Azure CLI:

    azure vm static-ip remove DNS01

Förväntad utdata:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Hur du lägger till en statisk privat IP-adress till en befintlig virtuell dator
Att lägga till en statisk privat IP-adressen till den virtuella datorn skapas med skriptet ovan runt han följande kommando:

    azure vm static-ip set DNS01 192.168.1.101

Förväntad utdata:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="set-ip-addresses-within-the-operating-system"></a>Ange IP-adresser inom operativsystemet

Vi rekommenderar att du inte statiskt tilldelar privata IP-Adressen som tilldelats den virtuella Azure-datorn i operativsystemet på en virtuell dator, om nödvändigt. Om du manuellt anger den privata IP-adressen i operativsystemet, kontrollera att det är samma adress som privat IP-adress som tilldelats den virtuella Azure-datorn eller du kan förlora anslutningen till den virtuella datorn. Du bör aldrig manuellt tilldela den offentliga IP-adress som tilldelats en virtuell Azure-dator i den virtuella datorns operativsystem.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [reserverade offentliga IP-Adressen](virtual-networks-reserved-public-ip.md) adresser.
* Lär dig mer om [instansnivå offentliga IP-går](virtual-networks-instance-level-public-ip.md) adresser.
* Läs den [reserverade IP-REST API: er](https://msdn.microsoft.com/library/azure/dn722420.aspx).


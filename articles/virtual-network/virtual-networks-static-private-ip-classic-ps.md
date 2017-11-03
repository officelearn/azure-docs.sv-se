---
title: "Konfigurera privata IP-adresser för virtuella datorer (klassisk) - Azure PowerShell | Microsoft Docs"
description: "Lär dig hur du konfigurerar den privata IP-adresser för virtuella datorer (klassisk) med hjälp av PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 60c7b489-46ae-48af-a453-2b429a474afd
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5da2992fad89a703086b7645c88f6d8e1a39e4b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-powershell"></a>Konfigurera privat IP-adresser för en virtuell dator (klassisk) med hjälp av PowerShell

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också [hantera en statisk privat IP-adress i Resource Manager-distributionsmodellen](virtual-networks-static-private-ip-arm-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Exempel PowerShell-kommandona nedan förväntar sig en enkel miljö som redan har skapats. Om du vill köra kommandon som de visas i det här dokumentet, först skapa testmiljön som beskrivs i [skapa ett VNet](virtual-networks-create-vnet-classic-netcfg-ps.md).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Så här kontrollerar du om det finns en specifik IP-adress
Kontrollera om IP-adressen *192.168.1.101* finns i ett VNet med namnet *TestVNet*, kör följande PowerShell-kommandot och kontrollera värdet för *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 

Förväntad utdata:

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Så här anger du en statisk privat IP-adress när du skapar en virtuell dator
PowerShell-skriptet nedan skapar en ny molntjänst med namnet *TestService*, hämtar sedan en avbildning från Azure, skapar en virtuell dator med namnet *DNS01* i ny molntjänst med den hämtade bilden anger den virtuella datorn till i ett undernät med namnet *klientdel*, och anger *192.168.1.7* som en statisk privat IP-adress för den virtuella datorn:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
      Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
      Set-AzureSubnet –SubnetNames FrontEnd |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      New-AzureVM -ServiceName TestService –VNetName TestVNet

Förväntad utdata:

    WARNING: No deployment found in service: 'TestService'.
    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
    New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hur du hämtar statisk privat IP-adressinformation för en virtuell dator
Kör följande PowerShell-kommando för att visa statisk privat IP-adressinformation för den virtuella datorn skapas med skriptet ovan, och notera att värdena för *IpAddress*:

    Get-AzureVM -Name DNS01 -ServiceName TestService

Förväntad utdata:

    DeploymentName              : TestService
    Name                        : DNS01
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 192.168.1.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : DNS01
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Ta bort en statisk privat IP-adress från en virtuell dator
Att ta bort statisk privat IP-adress till den virtuella datorn i skriptet ovan, kör följande PowerShell-kommando:

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Remove-AzureStaticVNetIP |
      Update-AzureVM

Förväntad utdata:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Hur du lägger till en statisk privat IP-adress till en befintlig virtuell dator
Att lägga till en statisk privat IP-adressen till den virtuella datorn skapas med skriptet ovan runt han följande kommando:

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      Update-AzureVM

Förväntad utdata:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [reserverade offentliga IP-Adressen](virtual-networks-reserved-public-ip.md) adresser.
* Lär dig mer om [instansnivå offentliga IP-går](virtual-networks-instance-level-public-ip.md) adresser.
* Läs den [reserverade IP-REST API: er](https://msdn.microsoft.com/library/azure/dn722420.aspx).


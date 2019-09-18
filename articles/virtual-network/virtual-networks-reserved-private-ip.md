---
title: Statisk intern privat IP – virtuell Azure-dator – klassisk
description: Förstå statiska interna IP-adresser (DIP) och hur du hanterar dem
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c37c49d8f7e09334014af290bf3a8c8e6d35f04b
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058358"
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Så här anger du en statisk intern privat IP-adress med PowerShell (klassisk)
I de flesta fall behöver du inte ange en statisk intern IP-adress för den virtuella datorn. Virtuella datorer i ett virtuellt nätverk tar automatiskt emot en intern IP-adress från ett intervall som du anger. Men i vissa fall är det bra att ange en statisk IP-adress för en viss virtuell dator. Om din virtuella dator till exempel kommer att köra DNS eller bli en domänkontrollant. En statisk intern IP-adress ligger kvar på den virtuella datorn även om steget stoppa/avetableras. 

> [!IMPORTANT]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser:  [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder [distributions modellen för Resource Manager](virtual-networks-static-private-ip-arm-ps.md).
> 
> 
> ## <a name="install-the-azure-powershell-service-management-module"></a>Installera modulen för Azure PowerShell Service Management

Innan du kör följande kommandon ska du kontrol lera att modulen [](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0
) för Azure PowerShell Service Management är installerad på datorn. Versions historiken för Azure PowerShell Service Management-modulen finns [i Azure-modulen i PowerShell-galleriet](https://www.powershellgallery.com/packages/Azure/5.3.0).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Så här kontrollerar du om en speciell IP-adress är tillgänglig
Kontrol lera att IP- *10.0.0.7* är tillgänglig i ett VNet med namnet *TestVnet*genom att köra följande PowerShell-kommando och kontrol lera värdet för *IsAvailable*.


    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Om du vill testa kommandot ovan i en säker miljö följer du rikt linjerna i [skapa ett virtuellt nätverk (klassiskt)](virtual-networks-create-vnet-classic-pportal.md) för att skapa ett virtuellt nätverk med namnet *TestVnet* och kontrollerar att det använder adress utrymmet *10.0.0.0/8* .
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Så här anger du en statisk intern IP-adress när du skapar en virtuell dator
PowerShell-skriptet nedan skapar en ny moln tjänst med namnet *TestService*och hämtar sedan en avbildning från Azure. därefter skapas en virtuell dator med namnet *testvm* i den nya moln tjänsten med hjälp av den hämtade avbildningen, som anger att den virtuella datorn finns i ett undernät med namnet *Subnet-1*. och anger *10.0.0.7* som en statisk intern IP-adress för den virtuella datorn:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Hämta statisk intern IP-information för en virtuell dator
Om du vill visa den statiska interna IP-informationen för den virtuella datorn som har skapats med skriptet ovan kör du följande PowerShell-kommando och observera värdena för *IpAddress*:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
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

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Så här tar du bort en statisk intern IP-adress från en virtuell dator
Om du vill ta bort den statiska interna IP-adressen som har lagts till i den virtuella datorn i skriptet ovan kör du följande PowerShell-kommando:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Så här lägger du till en statisk intern IP-adress till en befintlig virtuell dator
Om du vill lägga till en statisk intern IP-adress till den virtuella datorn som skapades med skriptet ovan kör du följande kommando:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Nästa steg
[Reserverad IP](virtual-networks-reserved-public-ip.md)

[Offentlig IP-adress på instans nivå (ILPIP)](virtual-networks-instance-level-public-ip.md)

[Reserverad IP REST-API: er](https://msdn.microsoft.com/library/azure/dn722420.aspx)


---
title: Statiska interna privata IP - Azure-dator – klassisk
description: Förstå statiska interna IP-adresser (DIPs) och hur du hanterar dem.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: b83a6e2c81eac9993c481561e3cebbed681d2c4a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640335"
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Hur du ställer in en statisk internt privat IP-adress med hjälp av PowerShell (klassisk)
I de flesta fall behöver du inte ange en statisk interna IP-adress för den virtuella datorn. Virtuella datorer i ett virtuellt nätverk får automatiskt en intern IP-adress från ett intervall som du anger. Men i vissa fall kan ange en statisk IP-adress för en viss virtuell dator är meningsfullt. Exempel: om den virtuella datorn är ska köra DNS eller kommer att vara en domänkontrollant. En statiska interna IP-adressen förblir med den virtuella datorn även via ett stoppa/avetablering tillstånd. 

> [!IMPORTANT]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser:  [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder den [Resource Manager-distributionsmodellen](virtual-networks-static-private-ip-arm-ps.md).
> 
> 
> ## <a name="install-the-azure-powershell-service-management-module"></a>Installera Azure PowerShell Service Management-modulen

Innan du kör följande kommandon, kontrollerar du att den [Azure PowerShell Service Management-modulen](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0
) är installerad på datorn. Versionshistorik för Azure PowerShell Service Management-modulen, se [Azure-modulen i PowerShell-galleriet](https://www.powershellgallery.com/packages/Azure/5.3.0).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Så här verifierar du om en specifik IP-adress är tillgänglig
Kontrollera om IP-adressen *10.0.0.7* är tillgänglig i ett virtuellt nätverk med namnet *TestVnet*, kör följande kommando i PowerShell och kontrollera värdet för *IsAvailable*.


    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Om du vill testa kommandot ovan i en säker miljö följer du riktlinjerna i [skapa ett virtuellt nätverk (klassisk)](virtual-networks-create-vnet-classic-pportal.md) att skapa ett virtuellt nätverk med namnet *TestVnet* och se till att den använder den *10.0.0.0/8*  adressutrymme.
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Så här anger du en statiska interna IP-adress när du skapar en virtuell dator
PowerShell-skriptet nedan skapar en ny molntjänst med namnet *TestService*, sedan hämtar en avbildning från Azure och sedan skapar en virtuell dator med namnet *TestVM* i den nya Molntjänsten med hämtas avbildningen, ställer in den Virtuell dator i ett undernät med namnet *Subnet-1*, och anger *10.0.0.7* som en statiska interna IP-adress för den virtuella datorn:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Hur du hämtar statiska interna IP-information för en virtuell dator
Kör följande PowerShell-kommando för att visa statiska interna IP-information för den virtuella datorn skapas med skriptet ovan, och notera värdena för *IpAddress*:

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

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Ta bort en statiska interna IP-adress från en virtuell dator
Kör följande PowerShell-kommando för att ta bort den statiska interna IP-Adressen läggs till den virtuella datorn i skriptet ovan:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Lägga till en statiska interna IP-adress till en befintlig virtuell dator
Kör följande kommando för att lägga till en statiska interna IP-adress till den virtuella datorn skapas med skriptet ovan:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Nästa steg
[Reserverad IP-adress](virtual-networks-reserved-public-ip.md)

[Offentlig IP på instansnivå (ILPIP)](virtual-networks-instance-level-public-ip.md)

[Reserverade IP-REST API: er](https://msdn.microsoft.com/library/azure/dn722420.aspx)


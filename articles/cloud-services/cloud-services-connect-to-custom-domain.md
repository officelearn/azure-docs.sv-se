---
title: Ansluta en tjänst i molnet till en anpassad domänkontrollant | Microsoft Docs
description: Lär dig hur du ansluter din web/worker-roller till en anpassad AD-domän med PowerShell och AD Domain-tillägg
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 1e2d7c87-d254-4e7a-a832-67f84411ec95
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 8bee2e2038ee39c777e1ca09994ad21872d2029a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337348"
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Ansluta Azure Cloud Services-roller till en anpassad AD-domänkontrollanten i Azure
Vi kommer först ange ett virtuellt nätverk (VNet) i Azure. Vi kommer sedan att lägga till en Active Directory-domänkontrollant (som finns på en Azure-dator) till det virtuella nätverket. Nu ska vi ska lägga till befintliga molntjänstroller i förväg skapade VNet och sedan Anslut dem till domänkontrollanten.

Innan vi sätter igång, par saker att tänka på:

1. Den här kursen används PowerShell, så se till att du har Azure PowerShell installerad och klar att börja. Om du behöver hjälp med att konfigurera Azure PowerShell, se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).
2. Dina AD-domänkontrollant och Web/Worker-roll-instanser måste vara i det virtuella nätverket.

Följ den här stegvisa guiden och om du stöter på problem, oss lämna en kommentar i slutet av artikeln. Någon kontaktar du (Ja, vi läser kommentarer).

Det nätverk som refereras till av Molntjänsten måste vara en **klassiskt virtuellt nätverk**.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Du kan skapa ett virtuellt nätverk i Azure med Azure portal eller PowerShell. Den här självstudien används PowerShell. Om du vill skapa ett virtuellt nätverk med Azure-portalen, [skapa ett virtuellt nätverk](../virtual-network/quick-create-portal.md). Artikeln visas hur du skapar ett virtuellt nätverk (Resource Manager), men du måste skapa ett virtuellt nätverk (klassisk) för molntjänster. Gör i portalen, Välj **skapa en resurs**, typ *virtuellt nätverk* i den **Search** och tryck sedan på **RETUR**. I sökresultaten under **allt**väljer **virtuellt nätverk**. Under **Välj en distributionsmodell**väljer **klassiska**och välj sedan **skapa**. Du kan sedan följa stegen i artikeln.

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
När du har slutfört konfigurationen av det virtuella nätverket, måste du skapa en AD-domänkontrollant. I den här självstudiekursen vill vi ställa in en AD-domänkontrollant på en Azure-dator.

Gör detta genom att skapa en virtuell dator via PowerShell med hjälp av följande kommandon:

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Flytta upp den virtuella datorn till en domänkontrollant
Om du vill konfigurera den virtuella datorn som en AD-domänkontrollant, behöver du logga in på den virtuella datorn och konfigurera den.

Du kan hämta RDP-fil via PowerShell, använder du följande kommandon för att logga in till den virtuella datorn:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

När du har loggat in till den virtuella datorn, konfigurera den virtuella datorn som en AD-domänkontrollant genom att följa en stegvis guide på [hur du ställer in din kund AD-domänkontrollant](https://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Lägg till din molntjänst till det virtuella nätverket
Nu ska behöver du lägga till din molntjänstdistribution till det nya virtuella nätverket. Gör detta genom att ändra din cloud service cscfg genom att lägga till de relevanta avsnitten din cscfg med hjälp av Visual Studio eller valfri redigerare.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

Sedan skapar ditt projekt för cloud services och distribuera den till Azure. Om du behöver hjälp med att distribuera din cloud services-paket till Azure, se [hur du skapar och distribuerar en tjänst i molnet](cloud-services-how-to-create-deploy-portal.md)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Ansluta web/worker-roller till domänen
När ditt molntjänstprojekt distribueras på Azure kan ansluta dina rollinstanser till den anpassade AD-domänen med hjälp av AD-domän-tillägget. Om du vill lägga till tillägget AD-domän i din befintliga cloud services-distribution och ansluter till den anpassade domänen, kör du följande kommandon i PowerShell:

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

Och det är allt.

Cloud services ska vara ansluten till din anpassade domänkontrollant. Om du vill ha mer information om de olika tillgängliga alternativen att konfigurera AD Domain-tillägget kan du använda PowerShell-hjälpen. Ett par exempel följande:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```

---
title: Ansluta en tjänst i molnet till en anpassad domänkontrollant | Microsoft Docs
description: Lär dig hur du ansluter web/worker-roller till en anpassad AD-domän med PowerShell och tillägg för AD-domänen
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 1e2d7c87-d254-4e7a-a832-67f84411ec95
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 4a50ae5e19ff9bf79b7f5361e5a274a2aba350f5
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
ms.locfileid: "29845663"
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Ansluta Azure Cloud Services-roller till en anpassad AD-domänkontrollant finns i Azure
Vi kommer först ställa in ett virtuellt nätverk (VNet) i Azure. Vi ska lägga till en Active Directory-domänkontrollant (som finns på en virtuell dator i Azure) VNet. Vi kommer därefter lägga till befintliga molntjänstroller i förväg skapade virtuella nätverk och Anslut dem till domänkontrollanten.

Innan vi börjar några saker att tänka på:

1. Den här kursen använder PowerShell, så kontrollera att du har Azure PowerShell installerad och gå vidare. Om du vill ha hjälp med att konfigurera Azure PowerShell, se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).
2. AD-domänkontrollant och roll för Web/Worker-instanser måste vara i virtuella nätverk.

Följ den här stegvisa guiden och om du stöter på problem oss lämna en kommentar i slutet av artikeln. Någon får tillbaka till dig (Ja, kan vi läsa kommentarer).

Nätverket som refereras av Molntjänsten måste vara en **klassiskt virtuellt nätverk**.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Du kan skapa ett virtuellt nätverk i Azure med Azure-portalen eller PowerShell. Den här självstudien används PowerShell. Om du vill skapa ett virtuellt nätverk med Azure portal finns [skapa ett virtuellt nätverk](../virtual-network/quick-create-portal.md). Artikeln beskriver hur du skapar ett virtuellt nätverk (Resource Manager), men du måste skapa ett virtuellt nätverk (klassiskt) för molntjänster. Gör i portalen, Välj **skapar du en resurs**, typen *virtuellt nätverk* i den **Sök** och tryck sedan på **RETUR**. I sökresultaten under **allt**väljer **för virtuella nätverk**. Under **Välj en distributionsmodell**väljer **klassiska**och välj **skapa**. Följ sedan instruktionerna i artikeln.

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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
När du har slutfört ställa in det virtuella nätverket, behöver du skapa en AD-domänkontrollant. Den här självstudiekursen kommer ska vi kunna installera en AD-domänkontrollant på en virtuell dator i Azure.

Om du vill göra det, skapar du en virtuell dator med hjälp av PowerShell med hjälp av följande kommandon:

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

Du kan hämta RDP-filen via PowerShell, Använd följande kommandon för att logga in till den virtuella datorn:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

När du är inloggad på den virtuella datorn, konfigurera den virtuella datorn som en AD-domänkontrollant genom att följa steg för steg-guide på [hur du ställer in kunden AD-domänkontrollant](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Lägg till Molntjänsten i det virtuella nätverket
Du måste sedan lägga till distributionen cloud service till det nya VNet. Gör detta genom att ändra din cloud service cscfg genom att lägga till de relevanta avsnitten din cscfg med hjälp av Visual Studio eller valfri redigerare.

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

Därefter bygga projektet cloud services och distribuera den till Azure. För att få hjälp med att distribuera paketet cloud services till Azure finns [hur du skapar och distribuerar en tjänst i molnet](cloud-services-how-to-create-deploy-portal.md)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Ansluta web/worker-roller till domänen
När ditt molntjänstprojekt har distribuerats på Azure, ansluta dina rollinstanser till den anpassa AD-domän med AD-domän. Om du vill lägga till AD-domän-tillägget på den befintliga distributionen i cloud services och ansluta till den anpassa domänen, kör du följande kommandon i PowerShell:

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

Och det är den.

Dina molntjänster ska vara anslutna till din anpassade-domänkontrollant. Om du vill veta mer om olika alternativ för hur du konfigurerar AD-domän, använder du PowerShell-hjälpen. Några exempel följande:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```

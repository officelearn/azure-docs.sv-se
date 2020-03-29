---
title: Ansluta en molntjänst till en anpassad domänkontrollant | Microsoft-dokument
description: Lär dig hur du ansluter dina webb-/arbetsroller till en anpassad AD-domän med PowerShell- och AD-domäntillägg
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: d40e392984d2675c748bda00c61cdaeb1c0932da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75387028"
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Ansluta Azure Cloud Services-roller till en anpassad AD-domänkontrollant som finns i Azure
Vi konfigurerar först ett virtuellt nätverk (VNet) i Azure. Vi lägger sedan till en Active Directory-domänkontrollant (som finns på en virtuell Azure-dator) i det virtuella nätverket. Därefter lägger vi till befintliga molntjänstroller till det förskapade virtuella nätverket och ansluter dem sedan till domänkontrollanten.

Innan vi börjar, några saker att tänka på:

1. Den här självstudien använder PowerShell, så se till att du har Azure PowerShell installerat och redo att gå. Information om hur du konfigurerar Azure PowerShell finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).
2. Ad-domänkontrollanten och webb-/arbetsrollinstanserna måste finnas i det virtuella nätverket.

Följ den här steg-för-steg-guiden och om du stöter på några problem, lämna oss en kommentar i slutet av artikeln. Någon kommer att återkomma till dig (ja, vi läser kommentarer).

Nätverket som refereras av molntjänsten måste vara ett **klassiskt virtuellt nätverk**.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Du kan skapa ett virtuellt nätverk i Azure med Azure-portalen eller PowerShell. För den här självstudien används PowerShell. Information om hur du skapar ett virtuellt nätverk med Azure-portalen finns i [Skapa ett virtuellt nätverk](../virtual-network/quick-create-portal.md). Artikeln handlar om att skapa ett virtuellt nätverk (Resource Manager), men du måste skapa ett virtuellt nätverk (Klassisk) för molntjänster. Om du vill göra det väljer du **Skapa en resurs**i portalen, skriver *virtuellt nätverk* i rutan **Sök** och trycker sedan på **Retur**. Välj **Virtuellt nätverk**under **Allt**i sökresultaten . Under **Välj en distributionsmodell**väljer du **Klassisk**och väljer sedan **Skapa**. Du kan sedan följa stegen i artikeln.

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
När du har konfigurerat det virtuella nätverket måste du skapa en AD-domänkontrollant. För den här självstudien kommer vi att konfigurera en AD-domänkontrollant på en virtuell Azure-dator.

Det gör du genom att skapa en virtuell dator via PowerShell med följande kommandon:

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

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Befordra din virtuella dator till en domänkontrollant
Om du vill konfigurera den virtuella datorn som en AD-domänkontrollant måste du logga in på den virtuella datorn och konfigurera den.

Om du vill logga in på den virtuella datorn kan du hämta RDP-filen via PowerShell och använda följande kommandon:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

När du har loggat in på den virtuella datorn konfigurerar du den virtuella datorn som ad-domänkontrollant genom att följa steg-för-steg-guiden om [Hur du konfigurerar din AD-domänkontrollant](https://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx)för kunden .

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Lägga till molntjänsten i det virtuella nätverket
Därefter måste du lägga till din molntjänstdistribution till det nya virtuella nätverket. För att göra detta, ändra din molntjänst cscfg genom att lägga till relevanta avsnitt till din cscfg med Visual Studio eller redaktören som du väljer.

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

Nästa bygga ditt molntjänstprojekt och distribuera det till Azure. Information om hur du skapar [och distribuerar en molntjänstpaket](cloud-services-how-to-create-deploy-portal.md) till Azure finns i Skapa och distribuera en molntjänst

## <a name="connect-your-webworker-roles-to-the-domain"></a>Ansluta webb-/arbetsrollerna till domänen
När ditt molntjänstprojekt har distribuerats på Azure ansluter du dina rollinstanser till den anpassade AD-domänen med AD-domäntillägget. Om du vill lägga till AD-domäntillägget i din befintliga molntjänstdistribution och ansluta till den anpassade domänen kör du följande kommandon i PowerShell:

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

Och sedan är du klar.

Dina molntjänster bör anslutas till din anpassade domänkontrollant. Om du vill veta mer om de olika alternativ som finns tillgängliga för hur du konfigurerar AD-domäntillägg använder du hjälpen för PowerShell. Ett par exempel följer:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```




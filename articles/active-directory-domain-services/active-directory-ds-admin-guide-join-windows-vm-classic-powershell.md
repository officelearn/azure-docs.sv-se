---
title: 'Azure Active Directory Domain Services: Administrationsguide | Microsoft Docs'
description: "Anslut en virtuell Windows-dator till en hanterad domän med hjälp av Azure PowerShell och den klassiska distributionsmodellen."
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 9143b843-7327-43c3-baab-6e24a18db25e
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: 1bb1546fb616131a1e1868a0d0610c4cad5d73e2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-powershell"></a>Anslut en virtuell dator med Windows Server till en hanterad domän med hjälp av PowerShell
> [!div class="op_single_selector"]
> * [Klassiska Azure-portalen – Windows](active-directory-ds-admin-guide-join-windows-vm.md)
> * [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)
>
>

<br>

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Azure AD Domain Services stöder för närvarande inte Resource Manager-modellen.
>
>

Dessa steg visar hur du anpassar en uppsättning Azure PowerShell-kommandon som kan skapa och förkonfigurera en Windows-baserad Azure virtuella med hjälp av en byggblock-metod. De här stegen kan du skapa en Windows-baserad Azure virtuella och ansluta den till en Azure AD Domain Services-hanterad domän.

Här följer en Fyll-i-tomrum metod för att skapa uppsättningar med Azure PowerShell. Den här metoden kan vara användbart om du är nybörjare på PowerShell eller om du vill veta vilka värden som du vill ange för lyckad konfiguration. Avancerade PowerShell-användare kan ta kommandon och ersätta deras egna värden för variabler (rader som börjar med ”$”).

Om du inte redan har gjort så, följ instruktionerna i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) att installera Azure PowerShell på den lokala datorn. Öppna en kommandotolk i Windows PowerShell.

## <a name="step-1-add-your-account"></a>Steg 1: Lägg till ditt konto
1. I PowerShell-Kommandotolken skriver **Add-AzureAccount** och på **RETUR**.
2. Skriv e-postadressen som är associerade med din Azure-prenumeration och klicka på **Fortsätt**.
3. Skriv in lösenordet för ditt konto.
4. Klicka på **logga in**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>Steg 2: Ange din prenumeration och storage-konto
Ange din Azure-prenumeration och storage-konto genom att köra dessa kommandon i Kommandotolken för Windows PowerShell. Ersätt allt inom citattecken, inklusive den < och > tecken, med rätt namn.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Du kan hämta rätt prenumerationsnamn från egenskapen SubscriptionName för utdata från den **Get-AzureSubscription** kommando. Du kan hämta rätt lagringskontonamnet från egenskapen Label för utdata från den **Get-AzureStorageAccount** kommando när du kör den **Välj AzureSubscription** kommando.

## <a name="step-3-step-by-step-walkthrough---provision-the-virtual-machine-and-join-it-to-the-managed-domain"></a>Steg 3: Steg för steg - etablera den virtuella datorn och ansluta den till den hanterade domänen
Det här är den motsvarande Azure PowerShell-kommando som vill skapa den virtuella datorn med tomma rader mellan varje block för läsbarhet.

Ange information om Windows-dator som ska etableras.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

InstanceSize-värden för D-, DS- eller G-serien virtuella datorer finns i [virtuell dator och Molntjänststorlekar för Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Ange information om den hanterade domänen.

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

Ange namnet på Molntjänsten.

    $svcname="Contoso100-test"

Ange namnet på det virtuella nätverket som den virtuella datorn ska anslutas. Kontrollera att den hanterade AAD-DS-domänen är tillgänglig i det här virtuella nätverket.

    $vnetname="MyPreviewVnet"

Välj VM-avbildning som används för att etablera den virtuella datorn.

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Konfigurera VM - namn på VM, instansstorleken & bilden som ska användas.

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Hämta lokal administratörsbehörighet för den virtuella datorn. Välj en stark lokala administratörslösenordet.

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

Skaffa autentiseringsuppgifter för ett användarkonto som hör till ' AAD DC-administratörsgruppen för att koppla en VM till den hanterade domänen. Ange inte namnet på en domän - exempelvis i vårt exempel kan vi ange ”bob” som användarnamn.

    $domainadmincred=Get-Credential –Message "Now type the name (DO NOT INCLUDE THE DOMAIN) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

Konfigurera den virtuella datorn – Ange domänen koppling krav & autentiseringsuppgifter som krävs.

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

Ange ett undernät för den virtuella datorn.

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

Valfritt: Peka på IP-adressen för domänen. Det här steget är inte nödvändigt om du ställer in IP-adresserna för Azure AD Domain Services hanterade domänen för att DNS-servrarna för det virtuella nätverket.

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

Nu kan etablera domänanslutna Windows VM.

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="script-to-provision-a-windows-vm-and-automatically-join-it-to-an-aad-domain-services-managed-domain"></a>Skript för att etablera en virtuell Windows-dator och automatiskt ansluta den till en AAD Domain Services-hanterad domän
Den här uppsättningen för PowerShell-kommando skapar en virtuell dator för en line-of-business-server som:

* Använder Windows Server 2012 R2 Datacenter-avbildning.
* Är en extra liten virtuell dator.
* Har namnet Contoso100-test.
* Automatiskt är domänanslutna till contoso100 hanterad domän.
* Har lagts till i samma virtuella nätverk som den hanterade domänen.

Det här är det fullständiga exempelskriptet för att skapa Windows-dator och ansluta den automatiskt till den hanterade domänen med Azure AD Domain Services.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

    $svcname="Contoso100-test"
    $vnetname="MyPreviewVnet"

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

    $domainadmincred=Get-Credential –Message "Now type the name (not including the domain) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services - komma igång-guide](active-directory-ds-getting-started.md)
* [Administrera en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-administer-domain.md)

---
title: Flera innehavare i Azure Stack
description: Lär dig hur du stöd för flera kataloger i Azure Active Directory i Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/6/2018
ms.author: patricka
ms.reviewer: bryanr
ms.openlocfilehash: 91d5f24eacff83f8c6263a20aaa08665871bfd04
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094027"
---
# <a name="multi-tenancy-in-azure-stack"></a>Flera innehavare i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan konfigurera Azure Stack för att hantera användare från flera Azure Active Directory (Azure AD)-klienter kan använda tjänster i Azure Stack. Tänk dig följande scenario:

- Du är tjänstadministratör för contoso.onmicrosoft.com, där Azure Stack är installerad.
- Mary är Katalogadministratör för fabrikam.onmicrosoft.com, där gästanvändare finns.
- Marys företaget tar emot IaaS och PaaS-tjänster från ditt företag och behöver användarna från gästkatalogen (fabrikam.onmicrosoft.com) kan logga in och använda Azure Stack-resurser i contoso.onmicrosoft.com.

Den här guiden innehåller de steg som krävs i samband med det här scenariot konfigurerar flera innehavare i Azure Stack. I det här scenariot måste du och Mary slutföra stegen för att aktivera användare från Fabrikam att logga in och använda tjänster från Azure Stack-distributioner i Contoso.  

## <a name="enable-multi-tenancy"></a>Aktivera flera innehavare

Det finns några krav att kompensera för innan du konfigurerar flera innehavare i Azure Stack:
  
 - Du och Mary måste samordna administrativa åtgärder för både den katalog som Azure Stack är installerat i (Contoso) och gästkatalogen (Fabrikam).  
 - Kontrollera att du har [installerat](azure-stack-powershell-install.md) och [konfigurerats](azure-stack-powershell-configure-admin.md) PowerShell för Azure Stack.
 - [Ladda ned Azure Stack Tools](azure-stack-powershell-download.md), och importera modulerna som Connect och identitet:

    ```PowerShell  
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

### <a name="configure-azure-stack-directory"></a>Konfigurera Azure Stack-katalogen

I det här avsnittet konfigurerar du Azure Stack för att tillåta inloggningar från Fabrikam Azure AD directory-klienter.

Publicera gäst-Directory-klient (Fabrikam) till Azure Stack genom att konfigurera Azure Resource Manager för att acceptera användare och tjänsthuvudnamn från gästen directory-klient.

Service-administratör för contoso.onmicrosoft.com kör följande kommandon.

```PowerShell  
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group. 
$location = "local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName
```

### <a name="configure-guest-directory"></a>Konfigurera gästkatalogen

När Azure Stack-administratör / operator har aktiverat Fabrikam-katalog som ska användas med Azure Stack, Mary måste registrera Azure Stack med Fabrikams directory-klient.

#### <a name="registering-azure-stack-with-the-guest-directory"></a>Registrerar Azure Stack med gäst-directory

Mary Katalogadministratör på Fabrikam kör följande kommandon i gästen directory fabrikam.onmicrosoft.com.

```PowerShell
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
```

> [!IMPORTANT]
> Om din Azure Stack-administratör som installerar nya tjänster eller uppdateringar i framtiden, kan du behöva köra skriptet igen.
>
> Kör skriptet igen när som helst för att kontrollera status för Azure Stack-program i din katalog.
>
> Om du har märkt problem med att skapa virtuella datorer i Managed Disks (presenteras i uppdateringen 1808), en ny **Disk Resource Provider** har lagts till, som kräver det här skriptet ska köras igen.

### <a name="direct-users-to-sign-in"></a>Dirigera användarna att logga in

Nu när du och Mary har slutfört stegen för att publicera Mary directory, kan Mary dirigera Fabrikam-användare att logga in.  Fabrikam-användare (det vill säga användare med suffixet fabrikam.onmicrosoft.com) logga in genom att besöka https://portal.local.azurestack.external.  

Mary dirigerar någon [utländska huvudnamn](../role-based-access-control/rbac-and-directory-admin-roles.md) i katalogen Fabrikam (det vill säga användare i katalogen Fabrikam utan fabrikam.onmicrosoft.com suffix) att logga in med https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.  Om de inte använder den här URL: en, de skickas till deras standardkatalog (Fabrikam) och ta emot ett meddelande om att deras administratörsgodkända inte har.

## <a name="disable-multi-tenancy"></a>Inaktivera flera innehavare

Om du inte längre vill att flera klienter i Azure Stack kan du inaktivera multitenans genom att göra följande steg i ordning:

1. Som administratör för gästkatalogen (Mary i det här scenariot) kör *avregistrera AzsWithMyDirectoryTenant*. Cmdlet: en avinstallerar alla Azure Stack-program från den nya katalogen.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Som tjänstadministratör i Azure Stack (du i det här scenariot) kör *avregistrera AzSGuestDirectoryTenant*. 

    ``` PowerShell  
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
    $ResourceGroupName = "system.local"
    
    Unregister-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
     -DirectoryTenantName $azureStackDirectoryTenant `
     -GuestDirectoryTenantName $guestDirectoryTenantToBeDecommissioned `
     -ResourceGroupName $ResourceGroupName
    ```

    > [!WARNING]
    > Inaktivera multitenans steg måste utföras i ordning. Steg #1 misslyckas om steg #2 har slutförts först.

## <a name="next-steps"></a>Nästa steg

- [Hantera delegerade providrar](azure-stack-delegated-provider.md)
- [Azure Stack viktiga begrepp](azure-stack-key-features.md)
- [Hantera användning och fakturering för Azure Stack som molntjänstleverantör](azure-stack-add-manage-billing-as-a-csp.md)
- [Lägga till klient för användning och fakturering i Azure Stack](azure-stack-csp-howto-register-tenants.md)

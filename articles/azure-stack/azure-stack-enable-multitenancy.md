---
title: Aktivera flera innehavare i Azure-stacken | Microsoft Docs
description: "Lär dig hur du stöd för flera Azure Active Directory-kataloger i Azure-stacken"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: bdf92b8b73dca55e819545913931c0a79a366486
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="enable-multi-tenancy-in-azure-stack"></a>Aktivera flera innehavare i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan konfigurera Azure-grupp om du vill hantera användare från flera klienter för Azure Active Directory (Azure AD) att använda tjänster i Azure-stacken. Exempelvis kan du studera följande scenario:

 - Du är den tjänstadministratör för contoso.onmicrosoft.com, där Azure-stacken är installerad.
 - Mary är Directory-administratör i fabrikam.onmicrosoft.com, där gästanvändare finns. 
 - Marys företagets tar emot IaaS och PaaS-tjänster från ditt företag och måste tillåta att användare från katalogen gäst (fabrikam.onmicrosoft.com) att logga in och använda Azure Stack resurser i contoso.onmicrosoft.com.

Den här guiden innehåller de steg som krävs i samband med det här scenariot kan du konfigurera flera innehavare i Azure-stacken.  I det här scenariot måste du och Mary slutföra steg för att aktivera användare från Fabrikam att logga in och använda tjänster från Azure Stack-distributionen i Contoso.  

## <a name="before-you-begin"></a>Innan du börjar
Det finns några krav att ta hänsyn till innan du konfigurerar flera innehavare i Azure Stack:
  
 - Du och Mary måste samordna administrativa åtgärder i både den katalog som Azure-stacken är installerad i (Contoso) och Gäst-katalogen (Fabrikam).  
 - Kontrollera att du har [installerat](azure-stack-powershell-install.md) och [konfigurerats](azure-stack-powershell-configure-admin.md) PowerShell för Azure-stacken.
 - [Hämtar verktyg för Azure-stacken](azure-stack-powershell-download.md), och importerar modulerna Connect och identitet:

    ````PowerShell
        Import-Module .\Connect\AzureStack.Connect.psm1
        Import-Module .\Identity\AzureStack.Identity.psm1
    ```` 
 - Mary kräver [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) åtkomst till Azure-stacken. 

## <a name="configure-azure-stack-directory"></a>Konfigurera Azure Stack directory
I det här avsnittet konfigurerar du Azure-grupp om du vill tillåta inloggningar från Fabrikam Azure AD directory-klienter.

### <a name="onboard-guest-directory-tenant"></a>Publicera gäst directory-klient
Nästa, publicera gäst-Directory-klient (Fabrikam) till Azure-stacken.  Det här steget konfigurerar Azure Resource Manager för att godkänna användare och tjänstens huvudnamn från gästen directory-klient.

````PowerShell
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location "local" `
 -ResourceGroupName $ResourceGroupName
````



## <a name="configure-guest-directory"></a>Konfigurera gäst directory
När du har slutfört stegen i katalogen Azure Stack måste Mary ge medgivande till Azure-stacken åtkomst till katalogen Gäst och registrera Azure stacken med gäst-katalogen. 

### <a name="registering-azure-stack-with-the-guest-directory"></a>Registrerar Azure stacken med gäst-directory
När gästen directory-administratören har angett ditt medgivande för Azure stackutrymme för att få åtkomst till katalogen Fabrikams, måste de registrera Azure Stack för Fabrikams directory-klient.

````PowerShell
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
````
## <a name="direct-users-to-sign-in"></a>Dirigera användarna att logga in
Nu när du och Mary har slutfört stegen för att publicera Mary directory, direkt Mary Fabrikam användare att logga in.  Fabrikam-användare (det vill säga användare med suffixet fabrikam.onmicrosoft.com) logga in genom att besöka https://portal.local.azurestack.external.  

Mary kommer att dirigera alla [externa säkerhetsobjekt](../active-directory/active-directory-understanding-resource-access.md) i katalogen Fabrikam (det vill säga användare i katalogen Fabrikam utan suffixet fabrikam.onmicrosoft.com) att logga in med https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.  Om de inte använder denna URL skickas till deras standardkatalogen (Fabrikam) och ett felmeddelande som säger att deras admin inte har godkänt.

## <a name="next-steps"></a>Nästa steg

- [Hantera delegerade providers](azure-stack-delegated-provider.md)
- [Azure Stack viktiga begrepp](azure-stack-key-features.md)

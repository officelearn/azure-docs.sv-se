---
title: Konfigurera Azure Stack-operatorn PowerShell-miljö | Microsoft Docs
description: Lär dig hur du konfigurerar operatorn Stack Azure PowerShell-miljö.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 37D9CAC9-538B-4504-B51B-7336158D8A6B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: mabrigg
ms.openlocfilehash: a8ab52de6c57e84bb2c90ce6bcf53ef1b92e30af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="configure-the-azure-stack-operators-powershell-environment"></a>Konfigurera Azure Stack-operatorn PowerShell-miljö

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan konfigurera Azure-grupp om du vill använda PowerShell för att hantera resurser, till exempel skapa erbjudanden, planer, kvoter och aviseringar. Det här avsnittet hjälper dig att konfigurera operator-miljö. Om du vill konfigurera PowerShell för användarens miljö finns i [konfigurera Azure Stack användarens PowerShell-miljö](user/azure-stack-powershell-configure-user.md) artikel.

## <a name="prerequisites"></a>Förutsättningar

Kör följande förutsättningar antingen från den [development kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), eller från en Windows-baserad extern klient om du är [anslutna via VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn): 

* Installera [Azure Stack-kompatibla Azure PowerShell-moduler](azure-stack-powershell-install.md).  
* Hämta den [verktyg som krävs för att arbeta med Azure Stack](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Konfigurera operator-miljön och logga in på Azure-stacken

Konfigurera Azure Stack-operator med PowerShell. Beroende på vilken typ av distribution, Azure AD eller AD FS, kör något av följande skript: ersätta Azure AD-tenantName, GraphAudience slutpunkt och ArmEndpoint värden med miljö konfigurationen.

### <a name="azure-active-directory-azure-ad-based-deployments"></a>Azure Active Directory (AD Azure) baserade distributioner

````powershell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# Get the value of your Directory Tenant ID
$TenantID = Get-AzsDirectoryTenantId -AADTenantName "<mydirectorytenant>.onmicrosoft.com" -EnvironmentName AzureStackAdmin

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````


### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Active Directory Federation Services (AD FS) baserade distributioner

````powershell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# Get the value of your Directory Tenant ID
$TenantID = Get-AzsDirectoryTenantId -ADFS -EnvironmentName AzureStackAdmin

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````

## <a name="test-the-connectivity"></a>Testa anslutningen

Nu när du har fått allt installation, ska vi använda PowerShell för att skapa resurser i Azure-stacken. Du kan till exempel skapa en resursgrupp för ett program och lägga till en virtuell dator. Använd följande kommando för att skapa en resursgrupp med namnet ”MyResourceGroup”:

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Nästa steg
* [Utveckla mallar för Azure-Stack](user/azure-stack-develop-templates.md)
* [Distribuera mallar med PowerShell](user/azure-stack-deploy-template-powershell.md)

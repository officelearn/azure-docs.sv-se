---
title: Konfigurera Azure Stack PowerShell | Microsoft Docs
description: Lär dig hur du konfigurerar Azure Stack PowerShell-miljö.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 86608ef8b3623682bd10498605f8b7b62c377ff1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076478"
---
# <a name="configure-the-azure-stack-powershell-environment"></a>Konfigurera Azure Stack PowerShell-miljö

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan konfigurera Azure-grupp om du vill använda PowerShell för att hantera resurser, till exempel skapa erbjudanden, planer, kvoter och aviseringar. Det här avsnittet hjälper dig att konfigurera operator-miljö.

## <a name="prerequisites"></a>Förutsättningar

Kör följande förutsättningar antingen från den [development kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), eller från en Windows-baserad extern klient om du är [anslutna via VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn): 

 - Installera [Azure Stack-kompatibla Azure PowerShell-moduler](azure-stack-powershell-install.md).  
 - Hämta den [verktyg som krävs för att arbeta med Azure Stack](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Konfigurera operator-miljön och logga in på Azure-stacken

Konfigurera Azure Stack-operator med PowerShell. Beroende på vilken typ av distribution, Azure AD eller AD FS, kör något av följande skript: ersätta Azure AD-tenantName, GraphAudience slutpunkt och ArmEndpoint värden med miljö konfigurationen.

### <a name="azure-active-directory-azure-ad-based-deployments"></a>Azure Active Directory (AD Azure) baserade distributioner

````PowerShell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````


### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Active Directory Federation Services (AD FS) baserade distributioner

````PowerShell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

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
 - [Utveckla mallar för Azure-Stack](user/azure-stack-develop-templates.md)
 - [Distribuera mallar med PowerShell](user/azure-stack-deploy-template-powershell.md)

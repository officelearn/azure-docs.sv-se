---
title: Ansluta till Azure Stack med PowerShell som operatör | Microsoft Docs
description: Lär dig hur du ansluter till Azure Stack med PowerShell som operatör
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
ms.date: 11/08/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: b961fac00ba43eb1b44acc46c6f60fa0f3a10877
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957086"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Ansluta till Azure Stack med PowerShell som operatör

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan konfigurera Azure Stack för att använda PowerShell för att hantera resurser, t.ex skapa erbjudanden, planer, kvoter och aviseringar. Det här avsnittet hjälper dig att konfigurera miljön för operatorn.

## <a name="prerequisites"></a>Förutsättningar

Kör följande från den [Utvecklingskit](./asdk/asdk-connect.md#connect-with-rdp) eller från en Windows-baserad externa klient om du är [är anslutna till ASDK via VPN](./asdk/asdk-connect.md#connect-with-vpn). 

 - Installera [Azure Stack-kompatibla Azure PowerShell-moduler](azure-stack-powershell-install.md).  
 - Ladda ned den [verktyg som krävs för att arbeta med Azure Stack](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Konfigurera operator-miljö och logga in på Azure Stack

Konfigurera miljön för Azure Stack-operatör med PowerShell. Kör något av följande skript: Ersätt Azure AD-tenantName, GraphAudience slutpunkt och ArmEndpoint värden med din egen miljökonfiguration.

````PowerShell  
    # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
    # To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $tenantId
````

## <a name="test-the-connectivity"></a>Testa anslutningen

Nu när du har allt installation, Använd PowerShell för att skapa resurser i Azure Stack. Du kan till exempel skapa en resursgrupp för ett program och lägga till en virtuell dator. Använd följande kommando för att skapa en resursgrupp med namnet **MyResourceGroup**.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Nästa steg

 - [Utveckla mallar för Azure Stack](user/azure-stack-develop-templates.md)
 - [Distribuera mallar med PowerShell](user/azure-stack-deploy-template-powershell.md)
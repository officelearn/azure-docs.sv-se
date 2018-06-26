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
ms.date: 06/22/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 74a5a9408a78dd0da12fb3f8ed721774030cc438
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36749869"
---
# <a name="configure-the-azure-stack-powershell-environment"></a>Konfigurera Azure Stack PowerShell-miljö

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan konfigurera Azure-grupp om du vill använda PowerShell för att hantera resurser, till exempel skapa erbjudanden, planer, kvoter och aviseringar. Det här avsnittet hjälper dig att konfigurera operator-miljö.

## <a name="prerequisites"></a>Förutsättningar

Kör följande förutsättningar antingen från den [development kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), eller från en Windows-baserad extern klient om du är [anslutna via VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

 - Installera [Azure Stack-kompatibla Azure PowerShell-moduler](azure-stack-powershell-install.md).  
 - Hämta den [verktyg som krävs för att arbeta med Azure Stack](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Konfigurera operator-miljön och logga in på Azure-stacken

Konfigurera Azure Stack-operator med PowerShell. Kör något av följande skript: ersätta Azure AD-tenantName, GraphAudience slutpunkt och ArmEndpoint värden med miljö konfigurationen.

````PowerShell  
    # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
    # To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
````

## <a name="test-the-connectivity"></a>Testa anslutningen

Nu när du har fått allt installation, använda PowerShell för att skapa resurser i Azure-stacken. Du kan till exempel skapa en resursgrupp för ett program och lägga till en virtuell dator. Använd följande kommando för att skapa en resursgrupp med namnet **MyResourceGroup**.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Nästa steg

 - [Utveckla mallar för Azure-Stack](user/azure-stack-develop-templates.md)
 - [Distribuera mallar med PowerShell](user/azure-stack-deploy-template-powershell.md)

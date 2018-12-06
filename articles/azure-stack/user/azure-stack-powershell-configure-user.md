---
title: Ansluta till Azure Stack med PowerShell som en användare | Microsoft Docs
description: Steg för att ansluta till användarens Azure Stack-instansen.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2018
ms.author: sethm
ms.reviewer: Balsu.G
ms.openlocfilehash: 7710a08fffed304d4d9dd9b036e4c00bcd02a9a9
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52965965"
---
# <a name="connect-to-azure-stack-with-powershell-as-a-user"></a>Ansluta till Azure Stack med PowerShell som en användare

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Den här artikeln ger dig stegen för att ansluta till din Azure Stack-instans. Du måste ansluta för att hantera Azure Stack-resurser med PowerShell. Du kan till exempel använda PowerShell för att prenumerera på erbjudanden, skapa virtuella datorer och distribuera Azure Resource Manager-mallar. för att köra PowerShell-cmdletar.

Att komma igång:
  - Kontrollera att du har kraven.
  - Ansluter till Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS). 
  - Registrera resursprovidrar.
  - Testa anslutningen.

## <a name="prerequisites"></a>Förutsättningar

Du kan konfigurera dessa krav från den [Utvecklingskit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), eller från en Windows-baserad externa klient om du är [är anslutna via VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Installera [Azure Stack-kompatibla Azure PowerShell-moduler](azure-stack-powershell-install.md).
* Ladda ned den [verktyg som krävs för att arbeta med Azure Stack](azure-stack-powershell-download.md).

Kontrollera att du ersätter följande skriptvariabler med värden från din Azure Stack-konfiguration:

- **Namn på Azure AD-klient**  
  Namnet på din Azure AD-klient som används för att hantera Azure Stack, till exempel yourdirectory.onmicrosoft.com.
- **Azure Resource Manager-slutpunkt**  
  För Azure Stack development kit det här värdet är inställt på https://management.local.azurestack.external. Kontakta tjänstleverantören om du vill få ut värde för integrerade Azure Stack-system.

## <a name="connect-with-azure-ad"></a>Ansluta till Azure AD

  ```PowerShell
  $AADTenantName = "yourdirectory.onmicrosoft.com"
  $ArmEndpoint = "https://management.local.azurestack.external"

  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
  $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantId
   ```

## <a name="connect-with-ad-fs"></a>Ansluta med AD FS

  ```PowerShell  
  $ArmEndpoint = "https://management.local.azurestack.external"

  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
  $tenantId = (invoke-restmethod "$($AuthEndpoint)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment

  $cred = get-credential

  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $tenantId `
     $cred = get-credential
  ```

## <a name="register-resource-providers"></a>Registrera resursprovidrar

Resursproviders registreras inte automatiskt för nya användarprenumerationer som inte har några resurser som distribueras via portalen. Du kan uttryckligen registrera en resursprovider genom att köra följande skript:

```PowerShell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>Testa anslutningen

När du har allt konfigurera, testa anslutningen genom att använda PowerShell för att skapa resurser i Azure Stack. Skapa en resursgrupp för ett program som ett test och Lägg till en virtuell dator. Kör följande kommando för att skapa en resursgrupp med namnet ”MyResourceGroup”:

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Nästa steg

- [Utveckla mallar för Azure Stack](azure-stack-develop-templates.md)
- [Distribuera mallar med PowerShell](azure-stack-deploy-template-powershell.md)
- Om du vill konfigurera PowerShell för operatorn molnmiljö avser den [konfigurera PowerShell-miljö för Azure Stack-operatör](../azure-stack-powershell-configure-admin.md) artikeln.
---
title: Konfigurera Azure Stack användarens PowerShell-miljö | Microsoft Docs
description: Konfigurera Azure Stack användarens PowerShell-miljö
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/15/2018
ms.author: mabrigg
ms.reviewer: Balsu.G
ms.openlocfilehash: 2655b682d35dd1879c649ed58d524ecd80808896
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Konfigurera Azure Stack användarens PowerShell-miljö

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Använd instruktionerna i den här artikeln för att konfigurera PowerShell-miljö för en användare i Azure-stacken.
Du kan använda PowerShell för att hantera Azure-stacken resurser när du konfigurerar miljön. Du kan till exempel använda PowerShell för att prenumerera på erbjudanden, skapa virtuella datorer och distribuera Azure Resource Manager-mallar.

>[!NOTE]
>Den här artikeln är begränsad för Azure-stacken användaren miljöer. Om du vill konfigurera PowerShell för operatorn molnmiljö avser den [konfigurera operatorn Stack Azure PowerShell-miljö](../azure-stack-powershell-configure-admin.md) artikel.

## <a name="prerequisites"></a>Förutsättningar

Du kan konfigurera dessa krav från den [development kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), eller från en Windows-baserad extern klient om du är [anslutna via VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Installera [Azure Stack-kompatibla Azure PowerShell-moduler](azure-stack-powershell-install.md).
* Hämta den [verktyg som krävs för att arbeta med Azure Stack](azure-stack-powershell-download.md).

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>Konfigurera användarmiljön och logga in på Azure-stacken

Beroende på vilken typ av distributionen Azure Stack (Azure AD eller AD FS), kör något av följande skript för att konfigurera PowerShell för Azure-stacken.

Kontrollera att du ersätter följande skriptvariabler med värden från konfigurationen av Azure Stack:

* AAD tenantName
* GraphAudience slutpunkt
* ArmEndpoint

### <a name="azure-active-directory-aad-based-deployments"></a>Azure Active Directory (AAD) baserade distributioner

  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID
   ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Active Directory Federation Services (AD FS) baserade distributioner

  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID
  ```

## <a name="register-resource-providers"></a>Registrera resursprovidrar

Resursproviders registreras inte automatiskt för nya användare prenumerationer som inte har några resurser som distribueras via portalen. Du kan registrera en resursleverantör genom att köra följande skript:

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>Testa anslutningen

När du har fått allt konfigurera testa anslutningen med hjälp av PowerShell för att skapa resurser i Azure-stacken. Skapa en resursgrupp för ett program som ett test och Lägg till en virtuell dator. Kör följande kommando för att skapa en resursgrupp med namnet ”MyResourceGroup”:

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Nästa steg

* [Utveckla mallar för Azure-Stack](azure-stack-develop-templates.md)
* [Distribuera mallar med PowerShell](azure-stack-deploy-template-powershell.md)

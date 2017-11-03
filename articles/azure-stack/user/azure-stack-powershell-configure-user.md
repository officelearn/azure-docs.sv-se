---
title: "Konfigurera Azure Stack användarens PowerShell-miljö | Microsoft Docs"
description: "Konfigurera Azure Stack användarens PowerShell-miljö"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: sngun
ms.openlocfilehash: e0ad968cac50ebb1e9ca0a4ff228c748f2da5f28
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2017
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Konfigurera Azure Stack användarens PowerShell-miljö

Du kan konfigurera din Azure-stacken Development Kit's PowerShell-miljö som en Azure-Stack-användare. När du har konfigurerat, du kan använda PowerShell för att hantera Azure-stacken resurser som prenumererar på erbjudanden, skapa virtuella datorer kan distribuera Azure Resource Manager-mallar, osv. Det här avsnittet är begränsad för att använda med användaren miljöer endast, om du vill konfigurera PowerShell för operatorn molnmiljö referera till den [konfigurera operatorn Stack Azure PowerShell-miljö](../azure-stack-powershell-configure-admin.md) avsnittet. 

## <a name="prerequisites"></a>Krav 

Kör följande förutsättningar antingen från den [development kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), eller från en Windows-baserad extern klient om du är [anslutna via VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Installera [Azure Stack-kompatibla Azure PowerShell-moduler](azure-stack-powershell-install.md).  
* Hämta den [verktyg som krävs för att arbeta med Azure Stack](azure-stack-powershell-download.md). 

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>Konfigurera användarmiljön och logga in på Azure-stacken

Beroende på vilken typ av distribution (Azure AD eller AD FS) som kör något av följande skript för att konfigurera PowerShell för Azure-stacken (se till att ersätta AAD tenantName, GraphAudience slutpunkt och ArmEndpoint värden enligt konfigurationen miljö):

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

När du arbetar på en nyligen skapade användaren prenumeration som inte har några resurser som distribueras via portalen registreras inte automatiskt resursleverantörer. Du bör explicit registrera dem med hjälp av följande skript:

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    } 
```

## <a name="test-the-connectivity"></a>Testa anslutningen

Nu när vi har allt är som vi ska använda PowerShell för att skapa resurser i Azure-stacken. Du kan till exempel skapa en resursgrupp för ett program och lägga till en virtuell dator. Använd följande kommando för att skapa en resursgrupp med namnet ”MyResourceGroup”:

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Nästa steg
* [Utveckla mallar för Azure-Stack](azure-stack-develop-templates.md)
* [Distribuera mallar med PowerShell](azure-stack-deploy-template-powershell.md)

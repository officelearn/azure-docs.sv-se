---
title: Installera och konfigurera PowerShell för Azure Stack-Snabbstart | Microsoft Docs
description: Läs mer om att installera och konfigurera PowerShell för Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 6996DFC1-5E05-423A-968F-A9427C24317C
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mabrigg
ms.openlocfilehash: 70c1fd72df437ade3bc12cd23db923f6d449e7fb
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38465751"
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Kom igång med PowerShell i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Den här snabbstarten hjälper dig att installera och konfigurera en Azure Stack-miljön med PowerShell. Skriptet som vi tillhandahåller i den här artikeln är begränsad till den **Azure Stack-operatör** endast.

Den här artikeln är en komprimerad version av stegen som beskrivs i den [installera PowerShell]( azure-stack-powershell-install.md), [ladda ned verktyg]( azure-stack-powershell-download.md), och [konfigurera Azure Stack-operatör PowerShell-miljö]( azure-stack-powershell-configure-admin.md) artiklar. Genom att använda skripten i det här avsnittet kan konfigurera du PowerShell för Azure Stack-miljöer som distribueras med Azure Active Directory eller Active Directory Federation Services (AD FS).  


## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Konfigurera PowerShell för Azure Active Directory-baserade distributioner

Logga in på Azure Stack Development Kit eller en extern Windows-baserad klient om du är ansluten via VPN. Öppna en upphöjd PowerShell ISE-session och kör sedan följande skript. Se till att uppdatera den **TenantName**, **ArmEndpoint**, och **GraphAudience** variabler som krävs för din miljökonfiguration:

```powershell
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $TenantName `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Add-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>Konfigurera PowerShell för AD FS-baserade distributioner

Du kan använda följande skript om du använder Azure Stack när du är ansluten till internet. Men om du använder Azure Stack utan Internetanslutning, använda den [frånkopplad sätt att installera PowerShell](azure-stack-powershell-install.md) och cmdletar för att konfigurera PowerShell förblir samma som visas i det här skriptet. Logga in på Azure Stack Development Kit eller en extern Windows-baserad klient om du är ansluten via VPN. Öppna en upphöjd PowerShell ISE-session och kör sedan följande skript. Se till att uppdatera den **ArmEndpoint** och **GraphAudience** variabler som krävs för din miljökonfiguration:

```powershell

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Add-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>Testa anslutningen

Nu när du har konfigurerat PowerShell kan testa du konfigurationen genom att skapa en resursgrupp:

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

> [!note]  
> Om du vill ange en resursgrupp, måste du ha en resursgrupp i din prenumeration. Mer information om prenumerationer finns i [översikt över Plan, erbjudande, kvot och prenumeration](azure-stack-plan-offer-quota-overview.md)

När resursgruppen har skapats kan den **Etableringsstatus** är inställd på **lyckades**.

## <a name="next-steps"></a>Nästa steg

* [Installera och konfigurera CLI](azure-stack-connect-cli.md)

* [Utveckla mallar](user/azure-stack-develop-templates.md)

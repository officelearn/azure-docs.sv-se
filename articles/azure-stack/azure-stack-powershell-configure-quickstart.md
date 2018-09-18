---
title: Installera och konfigurera PowerShell för Azure Stack-Snabbstart | Microsoft Docs
description: Läs mer om att installera och konfigurera PowerShell för Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: mabrigg
ms.openlocfilehash: 75b7f9c78418883344ce3c066135fe0847f649ac
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45981981"
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Kom igång med PowerShell i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Den här snabbstarten hjälper dig att installera och konfigurera en Azure Stack-miljön med PowerShell. Skriptet som vi tillhandahåller i den här artikeln är begränsad till den **Azure Stack-operatör** endast.

Den här artikeln är en komprimerad version av stegen som beskrivs i den [installera PowerShell]( azure-stack-powershell-install.md), [ladda ned verktyg]( azure-stack-powershell-download.md), och [konfigurera Azure Stack-operatör PowerShell-miljö]( azure-stack-powershell-configure-admin.md) artiklar. Genom att använda skripten i den här artikeln kan konfigurera du PowerShell för Azure Stack-miljöer som distribueras med Azure Active Directory eller Active Directory Federation Services (AD FS).  

## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Konfigurera PowerShell för Azure Active Directory-baserade distributioner  

Logga in på Azure Stack Development Kit eller en extern Windows-baserad klient om du är ansluten via VPN. Öppna en upphöjd PowerShell ISE-session och kör sedan följande skript.

Se till att uppdatera den **TenantName**, **ArmEndpoint**, och **GraphAudience** variabler som krävs för din miljökonfiguration:

```PowerShell  
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following commands:
Get-Module -ListAvailable -Name Azure* | Uninstall-Module
Get-Module Azs.* -ListAvailable | Uninstall-Module -force

# Install PowerShell for Azure Stack.
Install-Module -Name AzureRm.BootStrapper -Force
```

Läsa in modulen för API-profilen och administratör för din version av Azure Stack.

  - Azure Stack 1808 eller senare.

  ```PowerShell  
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.5.0 -Force
  ```

  - Azure Stack 1807 eller tidigare.

  ```PowerShell  
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force
    Install-Module -Name AzureStack -RequiredVersion 1.4.0 -Force
  ```

  - Azure Stack 1804 eller tidigare.

  ```PowerShell  
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force
    Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force
  ```

Ladda ned Azure Stack-verktyg och ansluta.

```PowerShell  
# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip -OutFile master.zip

expand-archive master.zip -DestinationPath . -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId -AADTenantName $TenantName -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>Konfigurera PowerShell för AD FS-baserade distributioner

Du kan använda följande skript om du använder Azure Stack när du är ansluten till internet. Men om du använder Azure Stack utan Internetanslutning, använda den [frånkopplad sätt att installera PowerShell](azure-stack-powershell-install.md) och cmdletar för att konfigurera PowerShell förblir samma som visas i det här skriptet. Logga in på Azure Stack Development Kit eller en extern Windows-baserad klient om du är ansluten via VPN. Öppna en upphöjd PowerShell ISE-session och kör sedan följande skript. Se till att uppdatera den **ArmEndpoint** och **GraphAudience** variabler som krävs för din miljökonfiguration:

```PowerShell  

# Set the module repository and the execution policy.
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module -Name AzureRm.BootStrapper -Force
```

Läsa in modulen för API-profilen och administratör för din version av Azure Stack.

  - Azure Stack 1808 eller senare.

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ````

  - Azure Stack 1807 eller tidigare.

    > [!Note]  
    Uppgradera från 1.2.11 version, finns i den [Migreringsguide](https://aka.ms/azspowershellmigration).

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.4.0
    ````

  - Azure Stack 1804 eller tidigare.

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.3.0
    ````

Ladda ned Azure Stack-verktyg och ansluta.

```PowerShell  
# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
https://github.com/Azure/AzureStack-Tools/archive/master.zip -OutFile master.zip

expand-archive master.zip -DestinationPath . -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId -ADFS -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>Testa anslutningen

Nu när du har konfigurerat PowerShell kan testa du konfigurationen genom att skapa en resursgrupp:

```PowerShell  
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

> [!note]  
> Om du vill ange en resursgrupp, måste du ha en resursgrupp i din prenumeration. Mer information om prenumerationer finns i [översikt över Plan, erbjudande, kvot och prenumeration](azure-stack-plan-offer-quota-overview.md)

När resursgruppen har skapats kan den **Etableringsstatus** är inställd på **lyckades**.

## <a name="next-steps"></a>Nästa steg

 - [Installera och konfigurera CLI](azure-stack-connect-cli.md)
 - [Utveckla mallar](user/azure-stack-develop-templates.md)

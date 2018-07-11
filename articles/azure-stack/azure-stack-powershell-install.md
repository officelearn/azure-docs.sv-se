---
title: Installera PowerShell för Azure Stack | Microsoft Docs
description: Lär dig mer om att installera PowerShell för Azure Stack.
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
ms.date: 07/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: e2785b0beeab042d4b1ad9a9eb5f545dbb58b8b9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38487509"
---
# <a name="install-powershell-for-azure-stack"></a>Installera PowerShell för Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Azure Stack-kompatibla Azure PowerShell-moduler som krävs för att arbeta med Azure Stack. I den här guiden vägleder vi dig igenom de steg som krävs för att installera PowerShell för Azure Stack.

Den här artikeln har detaljerade anvisningar för att installera PowerShell för Azure Stack.

> [!Note]  
> Följande steg kräver PowerShell 5.0. Du kan kontrollera din version genom att köra $PSVersionTable.PSVersion och jämföra den **större** version.

PowerShell-kommandon för Azure Stack installeras via PowerShell-galleriet. Du kan använda följande procedur för att verifiera om PSGallery registreras som en lagringsplats, öppna en upphöjd PowerShell-session och kör följande kommando:

```PowerShell  
Get-PSRepository -Name "PSGallery"
```

Om databasen inte är registrerad, öppna en upphöjd PowerShell-session och kör följande kommando:

```PowerShell  
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> Det här steget kräver Internetåtkomst. 

## <a name="uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>Avinstallera befintliga versioner av Azure Stack PowerShell-moduler

Innan du installerar versionen som krävs, se till att avinstallera eventuella tidigare installerade Azure Stack AzureRM PowerShell-moduler. Du kan avinstallera dem på något av följande två metoder:

 - Om du vill avinstallera de befintliga AzureRM PowerShell-modulerna att Stäng alla aktiva PowerShell-sessioner och kör följande kommando:

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - Ta bort alla mappar som börjar med ”Azure” från den `C:\Program Files\WindowsPowerShell\Modules` och `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` mappar. Tar bort dessa mappar tar bort alla befintliga PowerShell-moduler.

I följande avsnitt beskrivs de steg som krävs för att installera PowerShell för Azure Stack. PowerShell kan installeras på Azure Stack som drivs i kopplad, delvis ansluten eller i ett scenario med frånkopplade.

## <a name="install-the-azure-stack-powershell-modules-in-a-connected-scenario-with-internet-connectivity"></a>Installera Azure Stack PowerShell-moduler i ett scenario med anslutna (med Internet-anslutning)

Azure Stack kompatibla AzureRM-moduler installeras via API-versionsprofiler. Azure Stack kräver den **2017-03-09-profile** API-version-profil som är tillgängliga genom att installera modulen AzureRM.Bootstrapper. Mer information om API-versionsprofiler och de cmdletar som tillhandahålls av dem, referera till den [hantera API-versionsprofiler](user/azure-stack-version-profiles.md). Förutom AzureRM-moduler, bör du även installera Azure Stack-specifika PowerShell-moduler. Kör följande PowerShell-skript för att installera dessa moduler på utvecklingsdatorn:

  ```PowerShell  
# Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
Install-Module -Name AzureRm.BootStrapper 

# Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

# Install Module Version 1.3.0 if Azure Stack is running 1804 at a minimum 
Install-Module -Name AzureStack -RequiredVersion 1.3.0 

# Install Module Version 1.2.11 if Azure Stack is running a lower version than 1804 
Install-Module -Name AzureStack -RequiredVersion 1.2.11 
  ```

Bekräfta installationen kör du följande kommando:

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

Om installationen lyckas visas AzureRM- och AzureStack-moduler i utdata.

## <a name="install-the-azure-stack-powershell-modules-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Installera Azure Stack PowerShell-moduler i en frånkopplad eller en delvis scenariot (med begränsad Internetanslutning)

I ett scenario med frånkopplade måste du först hämta PowerShell-moduler till en dator som är ansluten till Internet och överför dem till Azure Stack Development Kit för installation.

> [!IMPORTANT]  
> Versionen av Azure Stack 1.3.0 PowerShell-modulen som levereras med en lista över större ändringar. Uppgradera från 1.2.11 version, finns i den [Migreringsguide](https://aka.ms/azspowershellmigration).

1. Logga in på en dator där du har Internetanslutning och använda följande skript för att ladda ned AzureRM och AzureStack paket på den lokala datorn:

   ```PowerShell  
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.3.0 
   ```

  > [!Important]  
  > Om du inte använder Azure Stack med update 1804 eller större, ändra den **requiredversion** parametervärde `1.2.11`. 

2. Kopiera de hämta paketen över till en USB-enhet.

3. Logga in på arbetsstationen och kopiera paket från USB-enheten till en plats på arbetsstationen.

4. Nu måste du registrera den här platsen som standard-databasen och installerar AzureRM- och AzureStack-moduler från den här lagringsplatsen:

   ```PowerShell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName 
   ```

## <a name="configure-powershell-to-use-a-proxy-server"></a>Konfigurera PowerShell för att använda en proxyserver

I scenarier som kräver en proxyserver för att få åtkomst till internet, måste du först konfigurera PowerShell för att använda en befintlig proxyserver.

1. Öppna en upphöjd PowerShell-prompt.
2. Kör följande kommandon:

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>Nästa steg

 - [Ladda ned Azure Stack-verktyg från GitHub](azure-stack-powershell-download.md)
 - [Konfigurera PowerShell-miljö för Azure Stack-användare](user/azure-stack-powershell-configure-user.md)  
 - [Konfigurera Azure Stack-operatör PowerShell-miljö](azure-stack-powershell-configure-admin.md) 
 - [Hantera API-versionsprofiler i Azure Stack](user/azure-stack-version-profiles.md)  

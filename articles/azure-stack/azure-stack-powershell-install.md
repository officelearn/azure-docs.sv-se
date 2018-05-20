---
title: Installera PowerShell för Azure-stacken | Microsoft Docs
description: Lär dig mer om att installera PowerShell för Azure-stacken.
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
ms.date: 5/17/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: cb672c8e378db80707db1b0cf77a3196e36b1eb5
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="install-powershell-for-azure-stack"></a>Installera PowerShell för Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Azure Stack kompatibla Azure PowerShell-moduler som krävs för att arbeta med Azure-stacken. I den här guiden vägleder vi dig igenom de steg som krävs för att installera PowerShell för Azure-stacken.

Den här artikeln har detaljerade anvisningar för att installera PowerShell för Azure-stacken.

> [!Note]
> Följande steg kräver PowerShell 5.0. Kontrollera din version genom att köra $PSVersionTable.PSVersion och jämföra den **större** version.

PowerShell-kommandon för Azure-stacken installeras via PowerShell-galleriet. Du kan använda följande procedur för att verifiera om PSGallery är registrerad som en databas, öppna en upphöjd PowerShell-session och kör följande kommando:

```PowerShell  
Get-PSRepository -Name "PSGallery"
```

Om databasen inte är registrerad, öppna en upphöjd PowerShell-session och kör följande kommando:

```PowerShell  
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> Det här steget kräver tillgång till Internet. 

## <a name="uninstall-existing-versions-of-powershell"></a>Avinstallera befintliga versioner av PowerShell

Kontrollera att avinstallera några tidigare installerade Azure Stack PowerShell-moduler innan du installerar versionen som krävs. Du kan avinstallera dem med hjälp av något av följande två metoder:

 - Om du vill avinstallera de befintliga PowerShell-modulerna, Stäng alla aktiva sessioner i PowerShell och kör följande kommando:

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - Ta bort alla mappar som börjar med ”Azure” från den `C:\Program Files\WindowsPowerShell\Modules` och `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` mappar. Tar bort dessa mappar tar bort några befintliga PowerShell-moduler.

I följande avsnitt beskrivs de steg som krävs för att installera PowerShell för Azure-stacken. PowerShell kan installeras på Azure-stacken som körs i ansluten, delvis ansluten eller i ett scenario med frånkopplade.

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>Installera PowerShell i ett scenario med anslutna (med Internetanslutning)

Azure Stack kompatibel AzureRM moduler installeras via API-version profiler. Azure-stacken kräver den **2017-03-09-profil** API-version profil som är tillgänglig genom att installera modulen AzureRM.Bootstrapper. Mer information om API-version profiler och de cmdlets som tillhandahålls av dem, referera till den [hantera profiler för API-version](user/azure-stack-version-profiles.md). Förutom AzureRM-moduler, bör du också installera Azure Stack-specifika PowerShell-moduler. Kör följande PowerShell-skript för att installera dessa moduler på utvecklingsdatorn:

  ```PowerShell  
# Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
Install-Module -Name AzureRm.BootStrapper 

# Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

# Install Module Version 1.3.0 if Azure Stack is running 1804 at a minimum 
Install-Module -Name AzureStack -RequiredVersion 1.3.0 

# Install Module Version 1.2.11 if Azure Stack is running a lower version then 1804 
Install-Module -Name AzureStack -RequiredVersion 1.2.11 
  ```

Om du vill bekräfta installationen genom att köra följande kommando:

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

Om installationen lyckas visas AzureRM och AzureStack-moduler i utdata.

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Installera PowerShell i ett frånkopplat eller delvis scenariot (med begränsad Internetanslutning)

I ett frånkopplat scenario måste du först ladda ned PowerShell-moduler för en dator som är ansluten till Internet och överför dem till Azure-stacken Development Kit för installation.

> [!IMPORTANT]  
> Versionen av Azure Stack 1.3.0 PowerShell-modulen innehåller en lista över bryta ändringar. Uppgradera från 1.2.11 version, finns det [Migreringsguide](https://aka.ms/azspowershellmigration).

1. Logga in på en dator där du har Internetanslutning och använda följande skript för att hämta AzureRM och AzureStack paket på den lokala datorn:

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
  > Om du inte kör Azure stacken med update 1804 eller större, ändrar du den **requiredversion** parametervärde för `1.2.11`. 

2. Kopiera de hämta paketen över till en USB-enhet.

3. Logga in på arbetsstationen och kopiera paket från USB-enheten till en plats på arbetsstationen.

4. Nu måste du registrera den här platsen som standard lagringsplatsen och installera modulerna AzureRM och AzureStack från den här lagringsplatsen:

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

## <a name="configure-powershell-to-use-a-proxy-server"></a>Konfigurera PowerShell om du vill använda en proxyserver

I scenarier som kräver en proxyserver för att få åtkomst till internet, måste du konfigurera PowerShell om du vill använda en befintlig proxyserver.

1. Öppna en upphöjd PowerShell-Kommandotolken.
2. Kör följande kommandon:

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>Nästa steg

 - [Hämta Azure Stack-verktyg från GitHub](azure-stack-powershell-download.md)
 - [Konfigurera Azure Stack användarens PowerShell-miljö](user/azure-stack-powershell-configure-user.md)  
 - [Konfigurera Azure Stack-operatorn PowerShell-miljö](azure-stack-powershell-configure-admin.md) 
 - [Hantera profiler för API-version i Azure-stacken](user/azure-stack-version-profiles.md)  

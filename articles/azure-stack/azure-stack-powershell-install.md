---
title: "Installera PowerShell för Azure-stacken | Microsoft Docs"
description: "Lär dig mer om att installera PowerShell för Azure-stacken."
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
ms.date: 10/13/2017
ms.author: sngun
ms.openlocfilehash: abbc1eac3ff1e8de90bd420dc1fcce7dbeda234c
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="install-powershell-for-azure-stack"></a>Installera PowerShell för Azure-stacken  

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Azure Stack kompatibla Azure PowerShell-moduler som krävs för att arbeta med Azure-stacken. I den här guiden vägleder vi dig igenom de steg som krävs för att installera PowerShell för Azure-stacken. Du kan använda stegen som beskrivs i den här artikeln från Azure Stack Development Kit eller från en extern Windows-baserad klient om du är ansluten via VPN.

Den här artikeln har detaljerade anvisningar för att installera PowerShell för Azure-stacken. Om du vill installera och konfigurera PowerShell snabbt du kan dock använda skriptet som har angetts i den [komma igång med PowerShell](azure-stack-powershell-configure-quickstart.md) avsnittet. 

> [!NOTE]
> Följande steg kräver PowerShell 5.0. Kontrollera din version genom att köra $PSVersionTable.PSVersion och jämföra ”” huvudversionen.

PowerShell-kommandon för Azure-stacken installeras via PowerShell-galleriet. Regiser PSGallery databasen, öppnar du en upphöjd PowerShell-session från development kit eller från en extern Windows-baserad klient om du är ansluten via VPN-anslutning och kör följande kommando:

```powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

## <a name="uninstall-existing-versions-of-powershell"></a>Avinstallera befintliga versioner av PowerShell

Kontrollera att avinstallera några befintliga Azure PowerShell-moduler innan du installerar versionen som krävs. Du kan avinstallera dem med hjälp av något av följande två metoder:

* Om du vill avinstallera de befintliga PowerShell-modulerna inloggningen i development kit eller för att den externa Windows-baserad klienten om du planerar att upprätta en VPN-anslutning. Stäng alla aktiva sessioner i PowerShell och kör följande kommando: 

   ```powershell
   Get-Module -ListAvailable | where-Object {$_.Name -like “Azure*”} | Uninstall-Module
   ```

* Logga in i development kit eller för att den externa Windows-baserad klienten om du planerar att upprätta en VPN-anslutning. Ta bort alla mappar som börjar med ”Azure” från den `C:\Program Files (x86)\WindowsPowerShell\Modules` och `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` mappar. Tar bort dessa mappar tar bort några befintliga PowerShell-moduler från ”AzureStackAdmin” och ”global” användare scope. 

I följande avsnitt beskrivs de steg som krävs för att installera PowerShell för Azure-stacken. PowerShell kan installeras på Azure-stacken som körs i ansluten, delvis ansluten eller i ett scenario med frånkopplade. 

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>Installera PowerShell i ett scenario med anslutna (med Internetanslutning)

Azure Stack kompatibel AzureRM moduler installeras via API-version profiler. Azure-stacken kräver den **2017-03-09-profil** API-version profil som är tillgänglig genom att installera modulen AzureRM.Bootstrapper. Mer information om API-version profiler och de cmdlets som tillhandahålls av dem, referera till den [hantera profiler för API-version](azure-stack-version-profiles.md). Förutom AzureRM-moduler, bör du också installera Azure Stack-specifika PowerShell-moduler. Kör följande PowerShell-skript för att installera dessa moduler på utvecklingsdatorn:

> [!IMPORTANT]
> Version av PowerShell-modulen AzureRM 1.2.11 levereras med en lista över bryta ändringar. Uppgradera från 1.2.10 versionen finns i Migreringsguide på [https://aka.ms/azspowershellmigration](https://aka.ms/azspowershellmigration).

  ```powershell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.11
  ```

Om du vill bekräfta installationen genom att köra följande kommando:

  ```powershell
  Get-Module `
    -ListAvailable | where-Object {$_.Name -like “Azure*”}
  ```
  Om installationen lyckas visas AzureRM och AzureStack-moduler i utdata.

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Installera PowerShell i ett frånkopplat eller delvis scenariot (med begränsad Internetanslutning)

I ett frånkopplat scenario måste du först ladda ned PowerShell-moduler för en dator som är ansluten till internet och överför dem till Azure-stacken Development Kit för installation.

> [!IMPORTANT]
> Version av PowerShell-modulen AzureRM 1.2.11 levereras med en lista över bryta ändringar. Uppgradera från 1.2.10 versionen finns i Migreringsguide på [https://aka.ms/azspowershellmigration](https://aka.ms/azspowershellmigration).

1. Logga in på en dator där du har Internetanslutning och använda följande skript för att hämta AzureRM och AzureStack paket på den lokala datorn:

   ```powershell
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
     -RequiredVersion 1.2.11 
   ```

2. Kopiera de hämta paketen över till en USB-enhet.

3. Logga in i development Kit och kopiera paket från USB-enheten till en plats på development kit. 

4. Nu måste du registrera den här platsen som standard lagringsplatsen och installera modulerna AzureRM och AzureStack från den här lagringsplatsen:

   ```powershell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   ```powershell
   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName 
   ```

## <a name="next-steps"></a>Nästa steg

* [Hämta Azure Stack-verktyg från GitHub](azure-stack-powershell-download.md)
* [Konfigurera Azure Stack användarens PowerShell-miljö](user/azure-stack-powershell-configure-user.md)  
* [Konfigurera Azure Stack-operatorn PowerShell-miljö](azure-stack-powershell-configure-admin.md) 
* [Hantera profiler för API-version i Azure-stacken](azure-stack-version-profiles.md)  

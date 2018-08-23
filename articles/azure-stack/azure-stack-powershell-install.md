---
title: Installera PowerShell för Azure Stack | Microsoft Docs
description: Lär dig mer om att installera PowerShell för Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 08/10/2018
ms.author: sethm
ms.reviewer: thoroet
ms.openlocfilehash: 2619f959dbefba84ea1a4d5aa974055998b78b5a
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42061122"
---
# <a name="install-powershell-for-azure-stack"></a>Installera PowerShell för Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Du måste installera Azure Stack kompatibla PowerShell-moduler att arbeta med molnet. Kompatibilitet aktiveras via en funktion som kallas *API profiler*.

API-profilerna ger ett sätt att hantera skillnaderna mellan Azure och Azure Stack. En profil för API-versionen är en uppsättning Azure Resource Managers PowerShell-moduler med specifika API-versioner. Varje molnplattform har en uppsättning API-versionsprofiler stöds. Till exempel Azure Stack stöd för en specifik daterat profil-version som **2017-03-09-profile**, och Azure har stöd för den **senaste** API version profil. När du installerar en profil kan installeras på Azure Resource Managers PowerShell-moduler som motsvarar den angivna profilen.

Du kan installera Azure Stack-kompatibla PowerShell-moduler i Internet ansluten, delvis ansluten, eller i ett scenario med frånkopplade. I den här artikeln vägleder vi dig igenom detaljerade anvisningar för att installera PowerShell för Azure Stack för dessa scenarier.

## <a name="1-verify-your-prerequisites"></a>1. Kontrollera ditt krav är uppfyllda

Innan din get igång med Azure Stack och PowerShell, behöver du har några krav på plats.

- **PowerShell-Version 5.0**  
Du kan kontrollera din version genom att köra $PSVersionTable.PSVersion och jämföra den **större** version. Om du inte har PowerShell 5.0, följer du de [länk](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) att uppgradera till PowerShell 5.0.

  > [!Note]  
  > PowerShell 5.0 kräver en Windows-dator.

- **Kör Powershell en förhöjd**  
  Du behöver för att kunna köra PowerShell med administratörsbehörighet.

- **PowerShell-galleriet åtkomst**  
  Du behöver åtkomst till den [PowerShell-galleriet](https://www.powershellgallery.com). Galleriet är en central lagringsplats för PowerShell-innehåll. Den **PowerShellGet** modulen innehåller cmdlets för identifiering, installera, uppdatera och publicera PowerShell artefakter som moduler, DSC-resurser, rollfunktioner och skript från PowerShell-galleriet och andra privata databaser. Om du använder PowerShell i ett scenario med frånkopplade, behöver du hämta resurser från en dator med en anslutning till Internet och lagra dem på en plats som är tillgängliga för frånkopplade datorn.


<!-- Nuget? -->

## <a name="2-validate-if-the-powershell-gallery-is-accessible"></a>2. Kontrollera om PowerShell-galleriet är tillgänglig

Kontrollera om PSGallery är registrerad som en databas.

> [!Note]  
> Det här steget kräver Internetåtkomst. 

Öppna en upphöjd PowerShell-kommandotolk och kör följande cmdlets:

````PowerShell  
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
````

Om databasen inte är registrerad, öppna en upphöjd PowerShell-session och kör följande kommando:

```PowerShell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. Avinstallera befintliga versioner av Azure Stack PowerShell-moduler

Innan du installerar versionen som krävs, se till att avinstallera eventuella tidigare installerade Azure Stack AzureRM PowerShell-moduler. Du kan avinstallera dem på något av följande två metoder:

1. Om du vill avinstallera de befintliga AzureRM PowerShell-modulerna, Stäng alla aktiva PowerShell-sessioner och kör följande cmdlets:

  ````PowerShell  
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ````

2. Ta bort alla mappar som börjar med `Azure` från den `C:\Program Files\WindowsPowerShell\Modules` och `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` mappar. Tar bort dessa mappar tar bort alla befintliga PowerShell-moduler.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. Ansluten: Installera PowerShell för Azure Stack med Internetanslutning

Azure Stack kräver den **2017-03-09-profile** API-version-profil som är tillgängliga genom att installera den **AzureRM.Bootstrapper** modulen. Förutom AzureRM-moduler, bör du även installera Azure Stack-specifika PowerShell-moduler. 

Kör följande PowerShell-skript för att installera dessa moduler på utvecklingsdatorn:

  - **Version 1.4.0** (Azure Stack 1804 eller senare)

    ```PowerShell  
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
    Install-Module -Name AzureRm.BootStrapper 

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

    # Install Module Version 1.4.0 if Azure Stack is running 1804 at a minimum 
    Install-Module -Name AzureStack -RequiredVersion 1.4.0
    ```

- **Version 1.2.11** (före 1804)

    ```PowerShell  
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
    Install-Module -Name AzureRm.BootStrapper 

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

    # Install Module Version 1.2.11 if Azure Stack is running a lower version than 1804 
    Install-Module -Name AzureStack -RequiredVersion 1.2.11 
    ```

Bekräfta installationen genom att köra följande kommando:

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

Om installationen lyckas visas AzureRM- och AzureStack-moduler i utdata.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Frånkopplad: Installera PowerShell utan Internetanslutning

I ett scenario med frånkopplade måste du först hämta PowerShell-moduler till en dator som är ansluten till Internet och överför dem till Azure Stack Development Kit för installation.

Logga in på en dator där du har Internetanslutning och använda följande skript för att ladda ned Azure Resource Manager och AzureStack paket till din lokala dator beroende på din version av Azure Stack.


  - **Version 1.3.0** (Azure Stack 1804 eller senare)
  
    > [!Note]  
    Uppgradera från 1.2.11 version, finns i den [Migreringsguide](https://aka.ms/azspowershellmigration).

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.4.0
    ````

  - **Version 1.2.11** (före 1804)

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"

      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
    ````

2. Kopiera de hämta paketen över till en USB-enhet.

3. Logga in på arbetsstationen och kopiera paket från USB-enheten till en plats på arbetsstationen.

4. Nu måste du registrera den här platsen som standard-databasen och installerar AzureRM- och AzureStack-moduler från den här lagringsplatsen:

   ```PowerShell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module AzureRM -Repository $RepoName

   Install-Module AzureStack -Repository $RepoName 
   ```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. Konfigurera PowerShell för att använda en proxyserver

I scenarier som kräver en proxyserver för att få åtkomst till Internet, måste du först konfigurera PowerShell för att använda en befintlig proxyserver.

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

---
title: "Med hjälp av API-version profiler i Azure-stacken | Microsoft Docs"
description: Mer information om API-version-profiler i Azure-stacken.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 68f4250c2a2a6bed1a1e21dc444e93cc87b6f59b
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Hantera profiler för API-version i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Profiler för API-versionen är ett sätt att hantera skillnaderna mellan Azure och Azure-stacken. En profil för API-versionen är en uppsättning AzureRM PowerShell-moduler med specifika API-versioner. Varje molnplattform har en uppsättning API-version profiler som stöds. Azure-stacken stöder till exempel en viss datum profil-version som **2017-03-09-profil**, och Azure stöder den **senaste** profil för API-version. När du installerar en profil installeras AzureRM PowerShell-moduler som motsvarar den angivna profilen.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Installera PowerShell-modulen som krävs för att använda profiler för API-version

Den **AzureRM.Bootstrapper** modul som finns tillgängliga via PowerShell-galleriet tillhandahåller PowerShell-cmdlets som krävs för att arbeta med profiler för API-version. Använd följande cmdlet för att installera modulen AzureRM.Bootstrapper:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```
Modulen AzureRM.Bootstrapper är en förhandsversion; detaljer och funktioner kan ändras. Om du vill hämta och installera den senaste versionen av den här modulen från PowerShell-galleriet, kör du följande cmdlet:

```PowerShell
Update-Module -Name "AzureRm.BootStrapper"
```

## <a name="install-a-profile"></a>Installera en profil

Använd den **installera AzureRmProfile** med den **2017-03-09-profil** API-version profilen för att installera AzureRM moduler som krävs av Azure-stacken. Observera att Azure-stacken operatorn moduler inte har installerats med den här profilen för API-version och de måste installeras separat som anges i steg3 i den [installera PowerShell för Azure-stacken](azure-stack-powershell-install.md) artikel.

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>Installera och importera moduler i en profil

Använd den **Använd AzureRmProfile** för att installera och importera moduler som är kopplad till en profil för API-version. Du kan importera endast en profil för API-version i PowerShell-sessionen. Om du vill importera en annan profil för API-version måste du öppna en ny PowerShell-session. Använd AzureRMProfile cmdleten körs följande uppgifter:  
1. Kontrollerar om PowerShell-moduler som är associerade med den angivna profilen för API-versionen är installerade i den aktuella omfattningen.  
2. Hämtar och installerar modulerna som om de inte redan är installerade.   
3. Importerar modulerna till den aktuella PowerShell-sessionen. 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

Om du vill installera och importerar valda AzureRM moduler från en profil för API-version, kör du cmdleten Använd AzureRMProfile med den **modulen** parameter:

```PowerShell
# Installs and imports the compute, Storage and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>Hämta installerade profiler

Använd den **Get-AzureRmProfile** för att hämta listan över tillgängliga profiler för API-version: 

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```
## <a name="update-profiles"></a>Uppdatera profiler

Använd den **uppdatering AzureRmProfile** att uppdatera modulerna i en profil för API-version till den senaste versionen av moduler som är tillgängliga i PSGallery. Vi rekommenderar att du alltid köra den **uppdatering AzureRmProfile** cmdlet i en ny PowerShell-session för att undvika konflikter när du importerar moduler. Uppdatera AzureRmProfile cmdlet körs följande uppgifter:

1. Kontrollerar om de senaste versionerna av moduler är installerade i profilen för angivna API-version för den aktuella omfattningen.  
2. Du uppmanas att installera om de inte redan är installerade.  
3. Installerar och importerar uppdaterade moduler till den aktuella PowerShell-sessionen.  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

Ta bort tidigare installerade versioner av modulerna innan du uppdaterar till den senaste tillgängliga versionen med cmdlet Update AzureRmProfile tillsammans med den **- RemovePreviousVersions** parameter:

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

Denna cmdlet körs följande uppgifter:  

1. Kontrollerar om de senaste versionerna av moduler är installerade i profilen för angivna API-version för den aktuella omfattningen.  
2. Tar bort äldre versioner av moduler från den aktuella profilen för API-version och i den aktuella PowerShell-sessionen.  
4. Du uppmanas att installera den senaste versionen.  
5. Installerar och importerar uppdaterade moduler till den aktuella PowerShell-sessionen.  
 
## <a name="uninstall-profiles"></a>Avinstallera profiler

Använd den **avinstallera AzureRmProfile** för att avinstallera den angivna profilen för API-version.

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>Nästa steg
* [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md)
* [Konfigurera Azure Stack användarens PowerShell-miljö](user/azure-stack-powershell-configure-user.md)  

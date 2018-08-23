---
title: Med hjälp av API-versionsprofiler med PowerShell i Azure Stack | Microsoft Docs
description: Lär dig mer om hur du använder API-versionsprofiler med PowerShell i Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 8da02641cc54f9308f8e0bbb8d2b28da9a930aa2
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42057658"
---
# <a name="use-api-version-profiles-for-powershell-in-azure-stack"></a>Använd API-versionsprofiler för PowerShell i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

API-versionsprofiler är ett sätt att hantera skillnaderna mellan Azure och Azure Stack. En profil för API-versionen är en uppsättning AzureRM PowerShell-moduler med specifika API-versioner. Varje molnplattform har en uppsättning API-versionsprofiler stöds. Till exempel Azure Stack stöd för en specifik daterat profil-version som **2017-03-09-profile**, och Azure har stöd för den **senaste** API version profil. När du installerar en profil installeras AzureRM PowerShell-moduler som motsvarar den angivna profilen.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Installera PowerShell-modulen som krävs för att använda API-versionsprofiler

Den **AzureRM.Bootstrapper** modulen som är tillgänglig via PowerShell-galleriet innehåller PowerShell-cmdlets som krävs för att arbeta med API-versionsprofiler. Använd följande cmdlet för att installera modulen AzureRM.Bootstrapper:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```

## <a name="install-a-profile"></a>Installera en profil

Använd den **Install-AzureRmProfile** cmdlet med den **2017-03-09-profile** API version profilen du installerar AzureRM-moduler som krävs av Azure Stack. Moduler för Azure Stack-operatör har inte installerats med den här profilen för API-version. De måste installeras separat som angetts i steg3 i den [installera PowerShell för Azure Stack](azure-stack-powershell-install.md) artikeln.

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>Installera och importera moduler i en profil

Använd den **Använd-AzureRmProfile** cmdlet för att installera och importera moduler som är associerade med en profil för API-version. Du kan importera endast en profil för API-versionen i en PowerShell-session. Om du vill importera en annan profil för API-version, måste du öppna en ny PowerShell-session. Använd-AzureRMProfile cmdleten körs följande uppgifter:  
1. Kontrollerar om PowerShell-moduler som är associerade med den angivna API-version-profilen är installerade i den aktuella omfattningen.  
2. Hämtar och installerar modulerna som om de inte redan är installerade.   
3. Importerar modulerna till den aktuella PowerShell-sessionen. 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

För att installera och importera valda AzureRM-moduler från en profil för API-version, kör du cmdleten Använd-AzureRMProfile med den **modulen** parameter:

```PowerShell
# Installs and imports the compute, Storage and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>Hämta installerade profiler

Använd den **Get-AzureRmProfile** cmdlet för att hämta listan över tillgängliga profiler för API-version: 

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```
## <a name="update-profiles"></a>Uppdatera profiler

Använd den **Update-AzureRmProfile** cmdlet för att uppdatera moduler i en profil för API-version till den senaste versionen av moduler som är tillgängliga i PSGallery. Vi rekommenderar att du alltid köra den **Update-AzureRmProfile** cmdlet i en ny PowerShell-session för att undvika konflikter när du importerar moduler. Cmdlet: en Update-AzureRmProfile körs följande uppgifter:

1. Kontrollerar om de senaste versionerna av moduler är installerade i profilen för angivna API-version för den aktuella omfattningen.  
2. Du uppmanas att installera om de inte redan är installerade.  
3. Installerar och importerar de uppdaterade modulerna till den aktuella PowerShell-sessionen.  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

Ta bort tidigare installerade versioner av modulerna innan du uppdaterar till den senaste tillgängliga versionen med cmdlet Update-AzureRmProfile tillsammans med den **- RemovePreviousVersions** parameter:

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

Denna cmdlet körs följande uppgifter:  

1. Kontrollerar om de senaste versionerna av moduler är installerade i profilen för angivna API-version för den aktuella omfattningen.  
2. Tar bort äldre versioner av moduler från den aktuella profilen för API-version och i den aktuella PowerShell-sessionen.  
4. Du uppmanas att installera den senaste versionen.  
5. Installerar och importerar de uppdaterade modulerna till den aktuella PowerShell-sessionen.  
 
## <a name="uninstall-profiles"></a>Avinstallera profiler

Använd den **Uninstall-AzureRmProfile** cmdlet för att avinstallera den angivna API-version-profilen.

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>Nästa steg
* [Installera PowerShell för Azure Stack](azure-stack-powershell-install.md)
* [Konfigurera PowerShell-miljö för Azure Stack-användare](azure-stack-powershell-configure-user.md)  

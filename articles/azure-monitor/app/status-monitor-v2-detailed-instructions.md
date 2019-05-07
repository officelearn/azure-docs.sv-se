---
title: Detaljerade instruktioner för Azure Status Monitor v2 | Microsoft Docs
description: Detaljerade anvisningar för att komma igång med Status Monitor v2. Övervaka prestanda på webbplatser utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 3aca64c7b0f1ad04967782cb3349da302db557a0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145093"
---
# <a name="status-monitor-v2-detailed-instructions"></a>Detaljerade instruktioner för status Monitor v2

Den här dokumentera information om hur att publicera till PowerShell-galleriet och hämta modulen ApplicationMonitor. Vi har dokumenterat de vanligaste parametrar som krävs för att komma igång.
Vi har även inkluderat manuella instruktionerna i händelse av att Internetåtkomst inte är tillgänglig.

> [!IMPORTANT]
> Status Monitor v2 är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="instrumentation-key"></a>Instrumentationsnyckel

Du måste ha en instrumenteringsnyckel för att komma igång. Mer information finns i [skapar en Application Insights-resurs](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-administrator-with-an-elevated-execution-policy"></a>Kör PowerShell som administratör med en upphöjd körning-princip

**Kör som administratör**: 
- Beskrivning: PowerShell behöver serverbehörigheter för administratören att göra ändringar i datorn.

**Körningsprincipen**:
- Beskrivning: Som standard kommer köra PowerShell-skript att inaktiveras. Vi rekommenderar att RemoteSigned skript för den aktuella omfattningen.
- Referens: [Om Körningsprinciper](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) och [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)
- Cmd: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`
- Valfria parametrar:
    - `-Force` Det här hoppar över bekräfta åtgärden.

**Exempel på fel:**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Krav för PowerShell

Granska din nuvarande version PowerShell genom att köra kommandot: `$PSVersionTable`.
Kommandot resulterar i följande utdata:


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Dessa instruktioner har skrivits och testats på en Windows 10-dator med de versioner som anges ovan.

## <a name="prerequisites-for-powershell-gallery"></a>Krav för PowerShell-galleriet

Dessa steg förbereder din server för att ladda ned moduler från PowerShell-galleriet.

> [!NOTE] 
> Stöd för PowerShell-galleriet ingår på Windows 10, Windows Server 2016 och PowerShell 6. Granska det här dokumentet för äldre versioner: [Installera PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)


1. Kör PowerShell som administratör med en princip för upphöjd körning.
2. NuGet-paketet-providern 
    - Beskrivning: Den här providern krävs för att interagera med NuGet-baserade databaser, till exempel PowerShellGallery
    - Referens: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6)
    - Cmd: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`
    - Valfria parametrar:
        - `-Proxy` Anger en proxyserver för begäran.
        - `-Force` Det här hoppar över bekräfta åtgärden. 
    
    Du får det här meddelandet om NuGet inte har ställts in:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Betrodda databaser
    - Beskrivning: Som standard är PowerShellGallery en ej betrodd lagringsplats.
    - Referens: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6)
    - Cmd: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`
    - Valfria parametrar:
        - `-Proxy` Anger en proxyserver för begäran.

    Du får det här meddelandet om PowerShell-galleriet är inte betrodd:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    - Kan bekräfta ändringen och granska alla PSRepositories genom att köra cmd: `Get-PSRepository`

4. PowerShellGet version 
    - Beskrivning: Den här modulen innehåller verktyg som används för att hämta andra moduler från PowerShell-galleriet. V1.0.0.1 levereras med Windows 10 och Windows Server. Lägsta version som krävs är v1.6.0. Granska vilken version är installerad kör kommandot: `Get-Command -Module PowerShellGet`
    - Referens: [Installera PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)
    - Cmd: `Install-Module -Name PowerShellGet`
    - Valfria parametrar:
        - `-Proxy` Anger en proxyserver för begäran.
        - `-Force` Detta ska ignorera varningen ”redan installerat” och installera den senaste versionen.

    Du får det här felet om du inte använder senaste versionen av PowerShellGet:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Starta om PowerShell. Det går inte att läsa in den nya versionen i den aktuella sessionen. Alla nya Powershell-sessioner kommer att ha de senaste PowerShellGet som lästs in.

## <a name="download--install-via-powershell-gallery"></a>Hämta och installera via PowerShell-galleriet

De här stegen hämtar modulen Az.ApplicationMonitor från PowerShell-galleriet.

1. Krav för PowerShell-galleriet krävs.
2. Kör PowerShell som administratör med en princip för upphöjd körning.
3. Installera modulen Az.ApplicationMonitor
    - Referens: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6)
    - Cmd: `Install-Module -Name Az.ApplicationMonitor`
    - Valfria parametrar:
        - `-Proxy` Anger en proxyserver för begäran.
        - `-AllowPrerelease` Detta gör att installera alpha och beta-versioner.
        - `-AcceptLicense` Detta kommer hoppa över uppmaningen ”Godkänn licens”
        - `-Force` Detta kommer att ignorera varningen ”ej betrodd lagringsplats”

## <a name="download--install-manually-offline-option"></a>Hämta och installera manuellt (offline alternativet)

Om du inte kan ansluta till PowerShell-modulen av någon anledning, kan du manuellt hämtar och installerar Az.ApplicationMonitor-modulen.

### <a name="manually-download-the-latest-nupkg"></a>Hämta den senaste nupkg manuellt

1. Gå till: https://www.powershellgallery.com/packages/Az.ApplicationMonitor
2. Välj den senaste versionen från tidigare versioner.
3. Hitta ”installationsalternativ” och välj ”manuell hämtning”.

### <a name="option-1-install-into-powershell-modules-directory"></a>Alternativ 1: Installera i PowerShell-moduler directory
Installera manuellt hämtade PowerShell-modulen till en PowerShell-katalog så att det kan vara identifierbar av PowerShell-sessioner.
Mer information finns i: [Installera en PowerShell-modul](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)


#### <a name="unzip-nupkg-as-zip-using-expand-archive-v1010"></a>Packa upp nupkg som zip med Expand-Arkiv (v1.0.1.0)

- Beskrivning: Den grundläggande versionen av Microsoft.PowerShell.Archive (v1.0.1.0) det går inte att packa upp nupkg filer. Byt namn på filen med tillägget ”.zip”.
- Referens: [Expandera Arkiv](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)
- Cmd: 

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-using-expand-archive-v1100"></a>Packa upp nupkg med Expand-Arkiv (v1.1.0.0).

- Beskrivning: Använd en aktuell version av Expandera Arkiv för att packa upp nupkgs utan att byta namn på tillägget. 
- Referens: [Expandera arkivet](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) och [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0)
- Cmd:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-manually"></a>Alternativ 2: packa upp och importera manuellt
Installera manuellt hämtade PowerShell-modulen till en PowerShell-katalog så att det kan vara identifierbar av PowerShell-sessioner.
Mer information finns i: [Installera en PowerShell-modul](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)

Om du installerar i en annan katalog, måste du manuellt importera modulen med hjälp av [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)

> [!IMPORTANT] 
> Installationen att installera DLL-filer via relativa sökvägar. Store innehållet i det här paketet i katalogen avsedda runtime och bekräfta att åtkomstbehörigheter tillåter Läs men inte skriva.

1. Ändra tillägget till ”.zip” och extrahera innehållet i paketet till din avsedda installationskatalog.
2. Hitta sökvägen till ”Az.ApplicationMonitor.psd1”.
3. Kör PowerShell som administratör med en princip för upphöjd körning. 
4. Läsa in modulen med kommandot: `Import-Module Az.ApplicationMonitor.psd1`.
    

## <a name="proxy"></a>Proxy

När du övervakar en dator i ett privat intranät, kan det vara nödvändigt att dirigera http-trafik via en proxyserver.

PowerShell-kommandon för att ladda ned och installera Az.ApplicationMonitor från PowerShell-galleriet har stöd för en `-Proxy` parametern.
Granska anvisningarna ovan när skriva din installationsskript.

Application Insights SDK behöver att skicka telemetri om ditt program till Microsoft. Vi rekommenderar att konfigurera proxyinställningar för ditt program i din web.config. Se [Programinsikter vanliga frågor och svar: Proxy-genomströmning](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough) för mer information.


## <a name="enable-monitoring"></a>Aktivera övervakning 

Cmd: `Enable-ApplicationInsightsMonitoring`

Granska våra [API-referens](status-monitor-v2-api-enable-monitoring.md) för en detaljerad beskrivning av hur du använder denna cmdlet. 



## <a name="next-steps"></a>Nästa steg

 Visa telemetrin:

- [Utforska mått](../../azure-monitor/app/metrics-explorer.md) för att övervaka prestanda och användning
- [Sök efter händelser och loggar](../../azure-monitor/app/diagnostic-search.md) att diagnostisera problem
- [Analys](../../azure-monitor/app/analytics.md) för mer avancerade frågor
- [Skapa instrumentpaneler](../../azure-monitor/app/app-insights-dashboards.md)

 Lägg till mer telemetri:

- [Skapa webbtester](monitor-web-app-availability.md) att kontrollera att webbplatsen är aktiv.
- [Lägg till telemetri för webbklienten](../../azure-monitor/app/javascript.md) vill visa undantag från webbsidans kod och så att du spårningsanrop.
- [Lägg till Application Insights SDK i koden](../../azure-monitor/app/asp-net.md) så att du kan lägga till spårnings- och logganrop

Gör mer med Status Monitor v2:

- Använd vår guide om hur du [Felsök](status-monitor-v2-troubleshoot.md) statusövervakaren v2.

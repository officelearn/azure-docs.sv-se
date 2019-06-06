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
ms.openlocfilehash: d0960c749d74903acc778c0f21d5c49f380195ae
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734187"
---
# <a name="status-monitor-v2-detailed-instructions"></a>Status Monitor v2: Detaljerade anvisningar

Den här artikeln beskrivs hur att publicera till PowerShell-galleriet och hämta modulen ApplicationMonitor.
Det beskrivs de vanligaste parametrar som du kan behöva för att komma igång.
Den innehåller också manuell anvisningar om du inte har tillgång till internet.

> [!IMPORTANT]
> Status Monitor v2 är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan ett serviceavtal och det rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte finns stöd och vissa kan ha begränsad funktionalitet.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="get-an-instrumentation-key"></a>Hämta en instrumenteringsnyckel

För att komma igång behöver du en instrumenteringsnyckel. Mer information finns i [skapar en Application Insights-resurs](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Kör PowerShell som administratör med en princip för upphöjd körning

**Kör som administratör**

PowerShell måste ha behörighet på behörighet att göra ändringar på datorn.

**Körningsprincip**
- Beskrivning: Som standard inaktiveras köra PowerShell-skript. Vi rekommenderar att RemoteSigned skript för endast den aktuella omfattningen.
- Referens: [Om Körningsprinciper](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) och [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
).
- Kommandot: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Valfri parameter:
    - `-Force`. Kringgår bekräfta åtgärden.

**Exempelfel**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Krav för PowerShell

Granska din instans av PowerShell genom att köra den `$PSVersionTable` kommando.
Det här kommandot ger följande utdata:


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

Dessa instruktioner har skrivits och testats på en dator som kör Windows 10 och de versioner som anges ovan.

## <a name="prerequisites-for-powershell-gallery"></a>Krav för PowerShell-galleriet

Dessa steg förbereder din server för att ladda ned moduler från PowerShell-galleriet.

> [!NOTE] 
> PowerShell-galleriet stöds på Windows 10, Windows Server 2016 och PowerShell 6.
> Information om tidigare versioner finns i [installera PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).


1. Kör PowerShell som administratör med en princip för upphöjd körning.
2. Installera NuGet-paketet-providern.
    - Beskrivning: Du behöver den här providern kan interagera med NuGet-baserade databaser som PowerShell-galleriet.
    - Referens: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Kommandot: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Valfria parametrar:
        - `-Proxy`. Anger en proxyserver för begäran.
        - `-Force`. Kringgår bekräfta åtgärden.
    
    Du får det här meddelandet om NuGet inte har ställts in:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Konfigurera PowerShell-galleriet som en betrodd lagringsplats.
    - Beskrivning: PowerShell-galleriet är en ej betrodd lagringsplats som standard.
    - Referens: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Kommandot: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Valfri parameter:
        - `-Proxy`. Anger en proxyserver för begäran.

    Du får det här meddelandet om PowerShell-galleriet är inte betrodd:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    Du kan bekräfta ändringen och granska alla PSRepositories genom att köra den `Get-PSRepository` kommando.

4. Installera den senaste versionen av PowerShellGet.
    - Beskrivning: Den här modulen innehåller verktyg som används för att hämta andra moduler från PowerShell-galleriet. Version 1.0.0.1 levereras med Windows 10 och Windows Server. Version 1.6.0 eller senare eller senare krävs. Om du vill kontrollera vilken version som är installerad kör den `Get-Command -Module PowerShellGet` kommando.
    - Referens: [Installera PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).
    - Kommandot: `Install-Module -Name PowerShellGet`.
    - Valfria parametrar:
        - `-Proxy`. Anger en proxyserver för begäran.
        - `-Force`. Kringgår ”redan installerat” varningen och installerar den senaste versionen.

    Du får det här felet om du inte använder den senaste versionen av PowerShellGet:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Starta om PowerShell. Du kan inte läsa in den nya versionen i den aktuella sessionen. Nya PowerShell-sessioner laddas den senaste versionen av PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Ladda ned och installera modulen via PowerShell-galleriet

De här stegen hämtar modulen Az.ApplicationMonitor från PowerShell-galleriet.

1. Se till att alla krav för PowerShell-galleriet är uppfyllda.
2. Kör PowerShell som administratör med en princip för upphöjd körning.
3. Installera modulen Az.ApplicationMonitor.
    - Referens: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Kommandot: `Install-Module -Name Az.ApplicationMonitor`.
    - Valfria parametrar:
        - `-Proxy`. Anger en proxyserver för begäran.
        - `-AllowPrerelease`. Tillåter installation av alpha och beta-versioner.
        - `-AcceptLicense`. Kringgår ”Godkänn licens”-fråga
        - `-Force`. Kringgår varningen ”ej betrodd lagringsplats”.

## <a name="download-and-install-the-module-manually-offline-option"></a>Hämta och installera modulen manuellt (offline alternativet)

Om du inte kan ansluta till PowerShell-modulen av någon anledning, kan du manuellt hämta och installera modulen Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg-file"></a>Hämta den senaste nupkg-filen manuellt

1. Gå till https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Välj den senaste versionen av filen i den **versionshistorik** tabell.
3. Under **installationsalternativ**väljer **manuell hämtning**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Alternativ 1: Installera i en katalog för PowerShell-moduler
Installera manuellt hämtade PowerShell-modulen i en PowerShell-katalog så att den blir synliga av PowerShell-sessioner.
Mer information finns i [installerar ett PowerShell-modulen](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Packa upp nupkg som en zip-fil med hjälp av Expandera-Arkiv (v1.0.1.0)

- Beskrivning: Den grundläggande versionen av Microsoft.PowerShell.Archive (v1.0.1.0) det går inte att packa upp nupkg filer. Byt namn på filen med filnamnstillägget .zip.
- Referens: [Expandera arkivet](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Kommandot:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Packa upp nupkg med Expand-Arkiv (v1.1.0.0)

- Beskrivning: Använda en aktuell version av Expandera Arkiv för att packa upp nupkg filer utan att ändra tillägget.
- Referens: [Expandera arkivet](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) och [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Kommandot:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Alternativ 2: Packa upp och importera nupkg manuellt
Installera manuellt hämtade PowerShell-modulen i en PowerShell-katalog så att den blir synliga av PowerShell-sessioner.
Mer information finns i [installerar ett PowerShell-modulen](https://docs.mircrosoft.com/powershell/developer/module/installing-a-powershell-module).

Om du installerar modulen till en annan katalog, manuellt importera modulen med hjälp av [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> DLL-filer installeras via relativa sökvägar.
> Store innehållet i paketet i katalogen avsedda runtime och bekräfta att åtkomstbehörigheter tillåter Läs men inte skriva.

1. Ändra tillägget till ”.zip” och extrahera innehållet i paketet till din avsedda installationskatalog.
2. Hitta sökvägen till Az.ApplicationMonitor.psd1.
3. Kör PowerShell som administratör med en princip för upphöjd körning.
4. Läsa in modulen med hjälp av den `Import-Module Az.ApplicationMonitor.psd1` kommando.
    

## <a name="route-traffic-through-a-proxy"></a>Dirigera trafik via en proxyserver

När du övervakar en dator i ett privat intranät, behöver du dirigera HTTP-trafik via en proxyserver.

Stöd för PowerShell-kommandon för att ladda ned och installera Az.ApplicationMonitor från PowerShell-galleriet en `-Proxy` parametern.
Granska av föregående anvisningar när du skriver din installationsskript.

Application Insights SDK måste du skicka din Apps telemetri till Microsoft. Vi rekommenderar att du konfigurerar proxyinställningarna för din app i din web.config-fil. Mer information finns i [Application Insights vanliga frågor och svar: Proxy-genomströmning](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Aktivera övervakning

Använd den `Enable-ApplicationInsightsMonitoring` kommando för att aktivera övervakning.

Se den [API-referens](status-monitor-v2-api-enable-monitoring.md) för en detaljerad beskrivning av hur du använder denna cmdlet.



## <a name="next-steps"></a>Nästa steg

 Visa telemetrin:

- [Utforska mått](../../azure-monitor/app/metrics-explorer.md) att övervaka prestanda och användning.
- [Sök efter händelser och loggar](../../azure-monitor/app/diagnostic-search.md) att diagnostisera problem.
- [Använda Analytics](../../azure-monitor/app/analytics.md) för mer avancerade frågor.
- [Skapa instrumentpaneler](../../azure-monitor/app/overview-dashboard.md).

 Lägg till mer telemetri:

- [Skapa webbtester](monitor-web-app-availability.md) att kontrollera att webbplatsen är aktiv.
- [Lägg till telemetri för webbklienten](../../azure-monitor/app/javascript.md) vill visa undantag från webbsidans kod och för att aktivera spårning av anrop.
- [Lägg till Application Insights SDK i koden](../../azure-monitor/app/asp-net.md) så att du kan lägga till spårnings- och logganrop.

Gör mer med Status Monitor v2:

- Använd vår guide om hur du [felsöka](status-monitor-v2-troubleshoot.md) statusövervakaren v2.

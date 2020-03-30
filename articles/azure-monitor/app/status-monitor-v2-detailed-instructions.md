---
title: Detaljerade instruktioner för Azure Application Insights Agent | Microsoft-dokument
description: Detaljerade instruktioner för hur du kommer igång med Application Insights Agent. Övervaka webbplatsens prestanda utan att distribuera om webbplatsen. Fungerar med ASP.NET webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: cd5ca5039b537859d5b31c901ed1f93877ecb629
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275729"
---
# <a name="application-insights-agent-formerly-named-status-monitor-v2-detailed-instructions"></a>Application Insights Agent (tidigare namnet Status Monitor v2): Detaljerade instruktioner

I den här artikeln beskrivs hur du går ombord till PowerShell-galleriet och hämtar Modulen ApplicationMonitor.
De vanligaste parametrarna som du behöver för att komma igång ingår.
Vi har också tillhandahållit manuella nedladdningsinstruktioner om du inte har tillgång till internet.

## <a name="get-an-instrumentation-key"></a>Hämta en instrumenteringsnyckel

För att komma igång behöver du en instrumenteringsnyckel. Mer information finns i [Skapa en application insights-resurs](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Kör PowerShell som administratör med en förhöjd körningsprincip

### <a name="run-as-admin"></a>Kör som administratör

PowerShell behöver behörighet på administratörsnivå för att kunna göra ändringar på datorn.
### <a name="execution-policy"></a>Policy för genomförande
- Beskrivning: Som standard är det inaktiverat att köra PowerShell-skript. Vi rekommenderar att du tillåter Fjärrsignerade skript för endast aktuellt scope.
- Referens: [Om körningsprinciper](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) och [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
).
- Kommando: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Valfri parameter:
    - `-Force`. Kringgår bekräftelseprompten.

**Exempel på fel**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Förutsättningar för PowerShell

Granska din instans av PowerShell genom att köra `$PSVersionTable` kommandot.
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

Dessa instruktioner skrevs och testades på en dator som kör Windows 10 och de versioner som anges ovan.

## <a name="prerequisites-for-powershell-gallery"></a>Förutsättningar för PowerShell-galleriet

De här stegen förbereder servern för att hämta moduler från PowerShell Gallery.

> [!NOTE] 
> PowerShell Gallery stöds i Windows 10, Windows Server 2016 och PowerShell 6.
> Information om tidigare versioner finns i [Installera PowerShellGet](/powershell/scripting/gallery/installing-psget).


1. Kör PowerShell som administratör med en förhöjd körningsprincip.
2. Installera NuGet-paketleverantören.
    - Beskrivning: Du behöver den här providern för att interagera med NuGet-baserade databaser som PowerShell Gallery.
    - Referens: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Kommando: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Valfria parametrar:
        - `-Proxy`. Anger en proxyserver för begäran.
        - `-Force`. Kringgår bekräftelseprompten.
    
    Den här frågan får du om NuGet inte har konfigurerats:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Konfigurera PowerShell Gallery som en betrodd databas.
    - Beskrivning: Som standard är PowerShell Gallery en ej betrodd databas.
    - Referens: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Kommando: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Valfri parameter:
        - `-Proxy`. Anger en proxyserver för begäran.

    Den här frågan får du om PowerShell Gallery inte är betrott:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    Du kan bekräfta den här ändringen och granska alla `Get-PSRepository` PSRepositories genom att köra kommandot.

4. Installera den senaste versionen av PowerShellGet.
    - Beskrivning: Den här modulen innehåller de verktyg som används för att hämta andra moduler från PowerShell-galleriet. Version 1.0.0.1 levereras med Windows 10 och Windows Server. Version 1.6.0 eller högre krävs. Om du vill ta reda `Get-Command -Module PowerShellGet` på vilken version som är installerad kör du kommandot.
    - Referens: [Installera PowerShellGet](/powershell/scripting/gallery/installing-psget).
    - Kommando: `Install-Module -Name PowerShellGet`.
    - Valfria parametrar:
        - `-Proxy`. Anger en proxyserver för begäran.
        - `-Force`. Kringgår varningen "redan installerat" och installerar den senaste versionen.

    Det här felet visas om du inte använder den senaste versionen av PowerShellGet:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Starta om PowerShell. Du kan inte läsa in den nya versionen i den aktuella sessionen. Nya PowerShell-sessioner läser in den senaste versionen av PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Ladda ned och installera modulen via PowerShell Gallery

Dessa steg hämtar Az.ApplicationMonitor-modulen från PowerShell Gallery.

1. Kontrollera att alla förutsättningar för PowerShell-galleriet uppfylls.
2. Kör PowerShell som administratör med en förhöjd körningsprincip.
3. Installera modulen Az.ApplicationMonitor.
    - Referens: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Kommando: `Install-Module -Name Az.ApplicationMonitor`.
    - Valfria parametrar:
        - `-Proxy`. Anger en proxyserver för begäran.
        - `-AllowPrerelease`. Tillåter installation av alfa- och betaversioner.
        - `-AcceptLicense`. Kringgår uppmaningen "Acceptera licens"
        - `-Force`. Kringgår varningen "Ej betrodd databas".

## <a name="download-and-install-the-module-manually-offline-option"></a>Hämta och installera modulen manuellt (offlinealternativ)

Om du av någon anledning inte kan ansluta till PowerShell-modulen kan du manuellt hämta och installera Az.ApplicationMonitor-modulen.

### <a name="manually-download-the-latest-nupkg-file"></a>Ladda ner den senaste nupkg-filen manuellt

1. Gå till https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Välj den senaste versionen av filen i tabellen **Versionshistorik.**
3. Under **Installationsalternativ**väljer du **Manuell nedladdning**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Alternativ 1: Installera i en PowerShell-modulkatalog
Installera den manuellt nedladdade PowerShell-modulen i en PowerShell-katalog så att den kan identifieras av PowerShell-sessioner.
Mer information finns i [Installera en PowerShell-modul](/powershell/scripting/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Packa upp nupkg som en zip-fil med hjälp av Expand-Archive (v1.0.1.0)

- Beskrivning: Basversionen av Microsoft.PowerShell.Archive (v1.0.1.0) kan inte packa upp nupkg-filer. Byt namn på filen med zip-tillägget.
- Referens: [Expandera-Arkiv](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Kommandot:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Packa upp nupkg med hjälp av Expandera-Arkiv (v1.1.0.0)

- Beskrivning: Använd en aktuell version av Expandera-Arkiv för att packa upp nupkg filer utan att ändra tillägget.
- Referens: [Expandera arkiv](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) och [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Kommandot:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Alternativ 2: Packa upp och importera nupkg manuellt
Installera den manuellt nedladdade PowerShell-modulen i en PowerShell-katalog så att den kan identifieras av PowerShell-sessioner.
Mer information finns i [Installera en PowerShell-modul](/powershell/scripting/developer/module/installing-a-powershell-module).

Om du installerar modulen i någon annan katalog importerar du modulen manuellt med [importmodulen](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> DLL-filer installeras via relativa sökvägar.
> Lagra innehållet i paketet i den avsedda körningskatalogen och bekräfta att åtkomstbehörigheter tillåter läsning men inte skrivning.

1. Ändra tillägget till ".zip" och extrahera innehållet i paketet till den avsedda installationskatalogen.
2. Hitta sökvägen till Az.ApplicationMonitor.psd1.
3. Kör PowerShell som administratör med en förhöjd körningsprincip.
4. Ladda modulen med `Import-Module Az.ApplicationMonitor.psd1` kommandot.
    

## <a name="route-traffic-through-a-proxy"></a>Dirigera trafik genom en proxy

När du övervakar en dator i ditt privata intranät måste du dirigera HTTP-trafik via en proxy.

PowerShell-kommandona för att hämta och installera Az.ApplicationMonitor `-Proxy` från PowerShell Gallery stöder en parameter.
Läs instruktionerna ovan när du skriver installationsskripten.

Application Insights SDK måste skicka appens telemetri till Microsoft. Vi rekommenderar att du konfigurerar proxyinställningar för din app i filen web.config. Mer information finns i [Vanliga frågor och svar om programinsikter: Proxygenomströmning](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Aktivera övervakning

Använd `Enable-ApplicationInsightsMonitoring` kommandot för att aktivera övervakning.

Se [API-referensen](status-monitor-v2-api-enable-monitoring.md) för en detaljerad beskrivning av hur du använder den här cmdleten.



## <a name="next-steps"></a>Nästa steg

 Visa telemetrin:

- [Utforska mätvärden](../../azure-monitor/app/metrics-explorer.md) för att övervaka prestanda och användning.
- [Sök händelser och loggar](../../azure-monitor/app/diagnostic-search.md) för att diagnostisera problem.
- [Använd Analytics](../../azure-monitor/app/analytics.md) för mer avancerade frågor.
- [Skapa instrumentpaneler](../../azure-monitor/app/overview-dashboard.md).

 Lägg till mer telemetri:

- [Skapa webbtester](monitor-web-app-availability.md) så att du är säker på att webbplatsen är aktiv.
- [Lägg till webbklienttelemetri](../../azure-monitor/app/javascript.md) för att se undantag från webbsidans kod och aktivera spårningsanrop.
- [Lägg till SDK för programinsikter i koden](../../azure-monitor/app/asp-net.md) så att du kan infoga spårnings- och loggsamtal.

Gör mer med Application Insights Agent:

- Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) Application Insights Agent.

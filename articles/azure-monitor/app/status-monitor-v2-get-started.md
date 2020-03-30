---
title: Azure Application Insights Agent – komma igång | Microsoft-dokument
description: En snabbstartsguide för Application Insights Agent. Övervaka webbplatsens prestanda utan att distribuera om webbplatsen. Fungerar med ASP.NET webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 7819de1f3dfab7f934421de86c0481d2e063f7a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671196"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Komma igång med Azure Monitor Application Insights Agent för lokala servrar

Den här artikeln innehåller snabbstartskommandon som förväntas fungera för de flesta miljöer.
Instruktionerna är beroende av PowerShell-galleriet för att distribuera uppdateringar.
Dessa kommandon stöder PowerShell-parametern. `-Proxy`

En förklaring av dessa kommandon, anpassningsinstruktioner och information om felsökning finns i [detaljerade instruktioner](status-monitor-v2-detailed-instructions.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="download-and-install-via-powershell-gallery"></a>Ladda ned och installera via PowerShell Gallery

### <a name="install-prerequisites"></a>Installationskrav
Kör PowerShell som administratör.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Stäng PowerShell.

### <a name="install-application-insights-agent"></a>Installera programstatistikagent
Kör PowerShell som administratör.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Aktivera övervakning
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Hämta och installera manuellt (offlinealternativ)
### <a name="download-the-module"></a>Ladda ner modulen
Hämta den senaste versionen av modulen manuellt från [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-application-insights-agent"></a>Packa upp och installera Application Insights Agent
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Aktivera övervakning
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



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

- Granska de [detaljerade instruktionerna](status-monitor-v2-detailed-instructions.md) för en förklaring av de kommandon som finns här.
- Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) Application Insights Agent.

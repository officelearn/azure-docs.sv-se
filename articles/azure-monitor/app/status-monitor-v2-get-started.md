---
title: Azure statusövervakaren v2 komma igång | Microsoft Docs
description: Snabbstartsguide för Status Monitor v2. Övervaka prestanda på webbplatser utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
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
ms.openlocfilehash: 3dcd50c3aa516f2af40c1e28a36a8039773e069c
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255066"
---
# <a name="getting-started-with-status-monitor-v2"></a>Komma igång med Status Monitor v2

Det här dokumentet innehåller quickstart-kommandon som förväntas användas i de flesta miljöer. Dessa instruktioner är beroende av PowerShell-galleriet för att distribuera uppdateringar. Dessa PowerShell-kommandona `-Proxy` parametern.

Granska våra [detaljerade instruktioner](status-monitor-v2-detailed-instructions.md) för en förklaring av dessa kommandon, instruktioner för hur du anpassar och hur du felsöker.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

> [!IMPORTANT]
> Status Monitor v2 är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="download--install-via-powershell-gallery"></a>Hämta och installera via PowerShell-galleriet

### <a name="install-prerequisites"></a>Installationskrav
Kör PowerShell som administratör
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Avsluta PowerShell

### <a name="install-status-monitor-v2"></a>Installationsstatus för övervaka v2
Kör PowerShell som administratör
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Aktivera övervakning
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download--install-manually-offline-option"></a>Hämta och installera manuellt (offline alternativet)
### <a name="manual-download"></a>Manuell hämtning
Hämta den senaste versionen av modulen från manuellt: https://www.powershellgallery.com/packages/Az.ApplicationMonitor

### <a name="unzip-and-install-status-monitor-v2"></a>Packa upp och installera Status Monitor v2
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.2.1-alpha.nupkg"
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

- [Utforska mått](../../azure-monitor/app/metrics-explorer.md) för att övervaka prestanda och användning
- [Sök efter händelser och loggar](../../azure-monitor/app/diagnostic-search.md) att diagnostisera problem
- [Analys](../../azure-monitor/app/analytics.md) för mer avancerade frågor
- [Skapa instrumentpaneler](../../azure-monitor/app/overview-dashboard.md)

 Lägg till mer telemetri:

- [Skapa webbtester](monitor-web-app-availability.md) att kontrollera att webbplatsen är aktiv.
- [Lägg till telemetri för webbklienten](../../azure-monitor/app/javascript.md) vill visa undantag från webbsidans kod och så att du spårningsanrop.
- [Lägg till Application Insights SDK i koden](../../azure-monitor/app/asp-net.md) så att du kan lägga till spårnings- och logganrop

Gör mer med Status Monitor v2:

- Granska den [detaljerade instruktioner](status-monitor-v2-detailed-instructions.md) för en förklaring av de kommandon som finns i den här guiden.
- Använd vår guide om hur du [Felsök](status-monitor-v2-troubleshoot.md) statusövervakaren v2.

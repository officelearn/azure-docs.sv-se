---
title: API-referens för Azure Application Insights-agent
description: Api-referens för Application Insights Agent. Inaktivera InstrumentationEngine. Övervaka webbplatsens prestanda utan att distribuera om webbplatsen. Fungerar med ASP.NET webbappar som finns lokalt, i virtuella datorer eller på Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: dafa12db95a10df467bf0d042cfd9395720648b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671383"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>Api för programinsikter agent: Disable-InstrumentationEngine

I den här artikeln beskrivs en cmdlet som är medlem i [modulen Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Beskrivning
Inaktiverar instrumenteringsmotorn genom att ta bort några registernycklar.
Starta om IIS för att ändringarna ska börja gälla.

> [!IMPORTANT] 
> Den här cmdleten kräver en PowerShell-session med administratörsbehörighet.

## <a name="examples"></a>Exempel

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Parametrar 

### <a name="-verbose"></a>-Utförlig
**Gemensam parameter.** Använd den här växeln för att mata ut detaljerade loggar.

## <a name="output"></a>Resultat


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Exempel på utdata från att framgångsrikt inaktivera instrumenteringsmotorn

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Nästa steg

 Gör mer med Application Insights Agent:
 - Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) Application Insights Agent.

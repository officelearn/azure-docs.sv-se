---
title: 'Azure Statusövervakare v2 API-referens: Inaktivera Instrumentation-motorn | Microsoft Docs'
description: API-referens för Statusövervakare v2. Disable-InstrumentationEngine. Övervaka webbplatsens prestanda utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 98f70a457b1e09f755a854a28cfd31a8fc9ee9f2
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033180"
---
# <a name="status-monitor-v2-api-disable-instrumentationengine"></a>API för Statusövervakare v2: Disable-InstrumentationEngine

Den här artikeln beskriver en cmdlet som är medlem i [PowerShell-modulen AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Beskrivning
Inaktiverar Instrumentation-motorn genom att ta bort vissa register nycklar.
Starta om IIS för att ändringarna ska börja gälla.

> [!IMPORTANT] 
> Denna cmdlet kräver en PowerShell-session med administratörs behörighet.

## <a name="examples"></a>Exempel

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Parametrar 

### <a name="-verbose"></a>– Utförlig
**Gemensam parameter.** Använd den här växeln för att skriva ut detaljerade loggar.

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Exempel på utdata från att inaktivera Instrumentation-motorn

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Nästa steg

 Gör mer med Statusövervakare v2:
 - Använd vår guide för att [felsöka](status-monitor-v2-troubleshoot.md) statusövervakare v2.

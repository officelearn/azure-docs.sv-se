---
title: 'Azure-Status Monitor v2 API-referens: Inaktivera instrumentation motorn | Microsoft Docs'
description: Status Monitor v2 API-referens Disable-InstrumentationEngine. Övervaka prestanda på webbplatser utan att omdistribuera webbplatsen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure.
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
ms.openlocfilehash: 8166ee1549355f165c7c86c99bc40afdc299c9e2
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870544"
---
# <a name="status-monitor-v2-api-disable-instrumentationengine-v021-alpha"></a>Status Monitor v2 API: Inaktivera InstrumentationEngine (v0.2.1-alfa)

Det här dokumentet beskriver en cmdlet som levereras som en medlem i den [Az.ApplicationMonitor PowerShell-modulen](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Status Monitor v2 är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Beskrivning

Denna cmdlet inaktiverar Instrumentation motorn genom att ta bort vissa registernycklar.
Starta om IIS för att ändringarna ska börja gälla.

> [!IMPORTANT] 
> Denna cmdlet kräver en PowerShell-Session med administratörsbehörighet.

## <a name="examples"></a>Exempel

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Parametrar 

### <a name="-verbose"></a>-Verbose
**Vanliga Parameter.** Använd den här växeln för att mata ut detaljerade loggar.

## <a name="output"></a>Resultat


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Exempel på utdata från har inaktiverar instrumentation motorn

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

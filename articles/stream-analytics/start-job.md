---
title: Starta ett Azure Stream Analytics-jobb
description: I den här artikeln beskrivs hur du startar ett Stream Analytics-jobb från Azure Portal, PowerShell och Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: c393eb782c2ff16eb5b3e5967b39938dfe2f1534
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426470"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Starta ett Azure Stream Analytics-jobb

Du kan starta ditt Azure Stream Analytics-jobb med Azure-portalen, Visual Studio och PowerShell. När du startar ett jobb väljer du en tid för jobbet att börja skapa utdata. Azure portal, Visual Studio och PowerShell har olika metoder för att ange starttid. Dessa metoder beskrivs nedan.

## <a name="start-options"></a>Startalternativ
De tre följande alternativen är tillgängliga för att starta ett jobb. Observera att alla tider som nämns nedan är de som anges i [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics). Om TIMESTAMP BY inte anges används ankomsttiden.
* **Nu**: Gör startpunkten för utdatahändelsen samma som när jobbet startas. Om en temporal operator används (t.ex. tidsfönster, LAG eller JOIN) kommer Azure Stream Analytics automatiskt att titta tillbaka på data i indatakällan. Om du till exempel startar ett jobb "Nu" och om din fråga använder ett 5-minuters tumlande fönster söker Azure Stream Analytics data från 5 minuter sedan i indata.
Den första möjliga utdatahändelsen skulle ha en tidsstämpel som är lika med eller större än den aktuella tiden, och ASA garanterar att alla indatahändelser som logiskt kan bidra till utdata har redovisats. Till exempel genereras inga partiella fönsterade aggregat. Det är alltid det fullständiga aggregerade värdet.

* **Anpassad:** Du kan välja startpunkt för utdata. På samma sätt som alternativet **Nu** läser Azure Stream Analytics automatiskt data före den här tiden om en temporal operator används 

* **När senast stoppas**. Det här alternativet är tillgängligt när jobbet startades tidigare, men stoppades manuellt eller misslyckades. När du väljer det här alternativet Azure Stream Analytics kommer att använda den senaste utdatatiden för att starta om jobbet så att inga data går förlorade. På samma sätt som tidigare alternativ läser Azure Stream Analytics automatiskt data före den här tiden om en temporal operator används. Eftersom flera indatapartitioner kan ha olika tid används den tidigaste stopptiden för alla partitioner, vilket resulterar i att vissa dubbletter kan ses i utdata. Mer information om exakt en gång bearbetning finns på sidan [Event Delivery Guarantees](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).


## <a name="azure-portal"></a>Azure Portal

Navigera till ditt jobb i Azure-portalen och välj **Start** på översiktssidan. Välj en **starttid för jobbutdata** och välj sedan **Start**.

Välj ett av alternativen för starttid för **jobbutdata**. Alternativen är *Nu*, *Anpassad*och, om jobbet kördes tidigare, När *senast stoppas*. Se ovan för mer information om dessa alternativ.

## <a name="visual-studio"></a>Visual Studio

Välj den gröna pilknappen i jobbvyn för att starta jobbet. Ange **startläge för jobbutdata** och välj **Start**. Jobbstatusen ändras till **Kör**.

Det finns tre alternativ för startläge för **jobbutdata:** *JobStartTime*, *CustomTime*och *LastOutputEventTime*. Om den här egenskapen saknas är standard *JobStartTime*. Se ovan för mer information om dessa alternativ.


## <a name="powershell"></a>PowerShell

Använd följande cmdlet för att starta jobbet med PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Det finns tre alternativ för **OutputStartMode:** *JobStartTime*, *CustomTime*och *LastOutputEventTime*. Om den här egenskapen saknas är standard *JobStartTime*. Se ovan för mer information om dessa alternativ.

Mer information om `Start-AzStreamAnalyitcsJob` cmdleten finns i [Start-AzStreamAnalyticsJob-referensen](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)
* [Snabbstart: Skapa ett Stream Analytics-jobb med Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure Stream Analytics-verktygen för Visual Studio](stream-analytics-quick-create-vs.md)

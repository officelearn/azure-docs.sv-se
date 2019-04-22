---
title: Hur du startar Azure Stream Analytics-jobb
description: Den här artikeln beskrivs hur du startar ett Stream Analytics-jobb.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 9bc3e4132919e5fc5baadc78841e66efd3c34bcd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59005939"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Hur du startar Azure Stream Analytics-jobb

Du kan börja din Azure Stream Analytics-jobb med Azure portal, Visual Studio och PowerShell. När du startar ett jobb, väljer du en gång för att jobbet ska börja skapa utdata. Azure-portalen, Visual Studio och PowerShell har olika metoder för att ange starttiden. Dessa metoder beskrivs nedan.

## <a name="start-options"></a>Starta alternativ
Följande tre alternativ är tillgängliga för att starta ett jobb. Observera att hela tiden som anges nedan är de som anges i [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics). Om TIMESTAMP BY inte anges, används ankomsttid.
* **Nu**: Gör startpunkt händelsens utdata strömma samma som när jobbet startas. Om du använder en temporal operator (t.ex. tidsfönster, FÖRDRÖJNING eller Anslut till), Azure Stream Analytics ska automatiskt gå tillbaka till data i Indatakällan. Om du startar ett jobb ”nu” och om din fråga använder 5 minuter rullande fönster, till exempel genomsöks Azure Stream Analytics data från 5 minuter sedan i indata.
Den första händelsen eventuella utdata skulle ha en tidsstämpel som är lika med eller större än den aktuella tiden och ASA garanterar att alla inkommande händelser som logiskt kan bidra till utdata är klara för. Exempelvis kan genereras inga partiella fönsteraggregeringar. Det är alltid fullständiga aggregerat värde.

* **Anpassat**: Du kan välja startpunkten för utdata. På samma sätt till den **nu** alternativ, Azure Stream Analytics läser automatiskt data före nu om du använder en temporal operator 

* **Senast stoppad**. Det här alternativet är tillgängligt när jobbet påbörjades tidigare, men stoppades manuellt eller misslyckades. När du väljer det här alternativet använder Azure Stream Analytics tid för senaste utdata för att starta om jobbet så att ingen data går förlorad. På samma sätt till föregående alternativ läser Azure Stream Analytics automatiskt data före nu om du använder en temporal operator. Eftersom flera inkommande partitioner kan ha olika tid, så snart som möjligt stopptiden för alla partitioner används kan därför vissa dubbletter kan ses i utdata. Mer information om exakt-när bearbetningen är tillgänglig på sidan [händelse Leveransgarantier](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).


## <a name="azure-portal"></a>Azure Portal

Gå till ditt jobb i Azure-portalen och välj **starta** på översiktssidan. Välj en **starttid för jobbutdata** och välj sedan **starta**.

Välj något av alternativen för **starttid för jobbutdata**. Alternativen är *nu*, *anpassade*, och om jobbet har körts tidigare, *senast stoppad*. Mer information om de här alternativen finns ovan.

## <a name="visual-studio"></a>Visual Studio

I jobbvyn, väljer du den gröna pilen för att starta jobbet. Ange den **jobbet utdata starta läget** och välj **starta**. Jobbstatusen ändras till **kör**.

Det finns tre alternativ för **jobbet utdata starta läget**: *JobStartTime*, *CustomTime*, och *LastOutputEventTime*. Om den här egenskapen inte finns, är standardvärdet *JobStartTime*. Mer information om de här alternativen finns ovan.


## <a name="powershell"></a>PowerShell

Använd följande cmdlet för att starta jobbet med hjälp av PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Det finns tre alternativ för **OutputStartMode**: *JobStartTime*, *CustomTime*, och *LastOutputEventTime*. Om den här egenskapen inte finns, är standardvärdet *JobStartTime*. Mer information om de här alternativen finns ovan.

Mer information om den `Start-AzStreamAnalyitcsJob` cmdlet, visa den [Start AzStreamAnalyticsJob referens](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)
* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure Stream Analytics-verktyg för Visual Studio](stream-analytics-quick-create-vs.md)

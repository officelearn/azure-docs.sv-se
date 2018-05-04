---
title: Felsöka händelsehubbmottagare i Azure Stream Analytics
description: Den här artikeln beskriver hur du använder flera konsumentgrupper för Händelsehubbar indata i Stream Analytics-jobb.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: aaa8c4e8d273b44f453d3f63f0be1d4baf980649
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2018
---
# <a name="troubleshoot-event-hub-receivers-in-azure-stream-analytics"></a>Felsöka händelsehubbmottagare i Azure Stream Analytics

Du kan använda Azure Händelsehubbar i Azure Stream Analytics infognings-eller utdata från ett jobb. Bästa praxis för att använda Händelsehubbar är att använda flera konsumentgrupper så jobbet skalbarhet. En orsak är att antalet läsare i Stream Analytics-jobbet för specifika indata påverkar antalet läsare i en enskild konsument-grupp. Det exakta antalet mottagare baseras på interna implementeringsinformation för logiken i skalbar topologi. Antalet mottagare exponeras inte externt. Antalet läsare kan ändra efter jobbets starttid eller under uppgraderingar av jobbet.

Felet visas när antalet mottagare överskrider maximalt är: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> När antalet läsare ändras under en uppgradering av jobb, skrivs tillfälligt varningar till granskningsloggar. Stream Analytics-jobb återskapa automatiskt från dessa tillfälliga problem.

## <a name="add-a-consumer-group-in-event-hubs"></a>Lägg till en konsumentgrupp i Händelsehubbar
Följ dessa steg för att lägga till en ny konsumentgrupp i Händelsehubbar-instans:

1. Logga in på Azure Portal.

2. Leta upp din Händelsehubbar.

3. Välj **Händelsehubbar** under den **entiteter** rubrik.

4. Välj Händelsehubben efter namn.

5. På den **Event Hubs instans** sidan under den **entiteter** rubrik, Välj **konsumentgrupper**. En konsumentgrupp med namnet **$Default** visas.

6. Välj **+ konsumentgrupp** att lägga till en ny konsumentgrupp. 

   ![Lägg till en konsumentgrupp i Händelsehubbar](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. När du skapade indata i Stream Analytics-jobbet så att den pekar till Händelsehubben angett du det konsumentgrupp. $Default används om inget anges. När du skapar en ny konsumentgrupp kan redigera Event Hub-indata i Stream Analytics-jobbet och ange namnet på den nya konsumentgruppen.


## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>Antalet läsare per partition överskrider Händelsehubbar gräns på fem

Om strömning frågesyntaxen refererar till samma Event Hub indataresursen flera gånger, kan jobb-motorn använda flera läsare per fråga från samma konsumentgrupp för. När det finns för många referenser till samma konsumentgrupp, jobbet kan överskrida gränsen på fem och genereras ett fel. I dessa fall kan dela du ytterligare med hjälp av flera inmatningar över flera konsumentgrupper med lösningen som beskrivs i följande avsnitt. 

Följande: scenarier där antalet läsare per partition är längre än Händelsehubbar fem

* Flera SELECT-satser: Om du använder flera SELECT-satser som refererar till **samma** händelsehubb indata, gör en ny mottagare som ska skapas för varje SELECT-sats.
* UNION: När du använder en UNION är det möjligt att ha flera indata som refererar till den **samma** händelsegruppen NAV- och konsumenter.
* SJÄLVKOPPLING: När du använder en SELF JOIN-åtgärd, är det möjligt att referera till den **samma** händelsehubb flera gånger.

## <a name="solution"></a>Lösning

Följande säkerhetsmetoder kan du minimera scenarier där antalet läsare per partition är längre än Händelsehubbar fem.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Dela din fråga i flera steg med hjälp av en WITH-satsen

WITH-satsen anger en tillfällig namngivna resultatmängd som kan refereras av en FROM-sats i frågan. Du definierar WITH-satsen i körningen omfånget för en enstaka SELECT-instruktion.

Till exempel i stället för den här frågan:

```
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Använd den här frågan:

```
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Kontrollera att indata ska bindas till olika konsumentgrupper

Skapa separata konsumentgrupper för frågor som tre eller flera inmatningar är anslutna till samma Händelsehubbar konsumentgruppen. Detta kräver att skapa ytterligare Stream Analytics-indata.


## <a name="next-steps"></a>Nästa steg
* [Skala Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Språkreferens för Stream Analytics-fråga](https://msdn.microsoft.com/library/azure/dn834998.aspx)

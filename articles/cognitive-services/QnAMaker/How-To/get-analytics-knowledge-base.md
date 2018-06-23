---
title: Få analyser på din knowledge base - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Få analyser på kunskapsbasen
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 1588d0c5a8eaf4e161b5319c9f33a772dc56b247
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354000"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Få analyser på kunskapsbasen

Frågor och svar om Maker lagrar alla chatt loggar och andra telemetri om du har aktiverat App Insights under den [skapa frågor och svar om Maker-tjänstens](./set-up-qnamaker-service-azure.md). Kör Exempelfrågor för att hämta chatt-loggar från appen insikter.

1. Gå till din App Insights-resurs.

    ![Välj din application insights-resurs](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Välj **Analytics**. En ny öppnas där du kan fråga frågor och svar om Maker telemetri.

    ![Välj Analytics](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. Klistra in i följande fråga och köra den.

    ```query
        requests
        | where url endswith "generateAnswer"
        | project timestamp, id, name, resultCode, duration
        | parse name with *"/knowledgebases/"KbId"/generateAnswer"
        | join kind= inner (
        traces | extend id = operation_ParentId
        ) on id
        | extend question = tostring(customDimensions['Question'])
        | extend answer = tostring(customDimensions['Answer'])
        | project KbId, timestamp, resultCode, duration, question, answer
    ```

    Välj **kör** att köra frågan.

    ![Kör frågan](../media/qnamaker-how-to-analytics-kb/run-query.png)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Köra frågor för andra analytics på frågor och svar om Maker kunskapsbasen

### <a name="total-90-day-traffic"></a>Totalt antal 90 dagars trafik

```query
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
``` 

### <a name="total-question-traffic-in-a-given-time-period"></a>Totalt antal fråga trafik i en viss tidsperiod

```query
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Användartrafik

```query
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse name with *"/knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId 
    ) on id
    | extend UserId = tostring(customDimensions['UserId'])
    | summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Fördelning av svarstid för frågor

```query
    //Latency distribution of questions
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera nycklar](./key-management.md)

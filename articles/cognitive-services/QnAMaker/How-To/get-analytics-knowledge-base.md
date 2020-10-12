---
title: Analys i kunskaps databas – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker lagrar alla chatt-loggar och annan telemetri, om du har aktiverat App Insights när du skapar din QnA Maker-tjänst. Kör exempel frågorna för att hämta dina chatt-loggar från App Insights.
services: cognitive-services
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/05/2019
ms.openlocfilehash: 00b7b88aa4ce0cab2a2379756e40054f27fc633b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87131658"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Få analyser om din kunskapsbas

QnA Maker lagrar alla chatt-loggar och annan telemetri, om du har aktiverat App Insights när du [skapar din QNA Maker-tjänst](./set-up-qnamaker-service-azure.md). Kör exempel frågorna för att hämta dina chatt-loggar från App Insights.

1. Gå till din App Insights-resurs.

    ![Välj din Application Insights-resurs](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Välj **logg (analys)**. Ett nytt fönster öppnas där du kan fråga QnA Maker telemetri.

3. Klistra in i följande fråga och kör den.

    ```kusto
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, url, resultCode, duration, performanceBucket
    | parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | extend score = tostring(customDimensions['Score'])
    | project timestamp, resultCode, duration, id, question, answer, score, performanceBucket,KbId
    ```

    Välj **Kör** för att köra frågan.

    [![Kör fråga för att fastställa frågor, svar och Poäng från användare](../media/qnamaker-how-to-analytics-kb/run-query.png)](../media/qnamaker-how-to-analytics-kb/run-query.png#lightbox)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Kör frågor för andra analyser på din QnA Maker kunskaps bas

### <a name="total-90-day-traffic"></a>Total trafik på 90 dagar

```kusto
//Total Traffic
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Total fråga trafik under en viss tids period

```kusto
//Total Question Traffic in a given time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
requests
| where timestamp <= endDate and timestamp >=startDate
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Användar trafik

```kusto
//User Traffic
requests
| where url endswith "generateAnswer"
| project timestamp, id, url, resultCode, duration
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend UserId = tostring(customDimensions['UserId'])
| summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Svars tids fördelning av frågor

```kusto
//Latency distribution of questions
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| project timestamp, id, name, resultCode, performanceBucket, KbId
| summarize count() by performanceBucket, KbId
```

### <a name="unanswered-questions"></a>Obesvarade frågor

```kusto
// Unanswered questions
requests
| where url endswith "generateAnswer"
| project timestamp, id, url
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend question = tostring(customDimensions['Question'])
| extend answer = tostring(customDimensions['Answer'])
| extend score = tostring(customDimensions['Score'])
| where  score  == "0" and message == "QnAMaker GenerateAnswer"
| project timestamp, KbId, question, answer, score
| order  by timestamp  desc
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Välj capactiy](./improve-knowledge-base.md)

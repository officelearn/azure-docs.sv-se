---
title: Analys i kunskaps databas – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker lagrar alla chatt-loggar och annan telemetri, om du har aktiverat App Insights när du skapar din QnA Maker-tjänst. Kör exempel frågorna för att hämta dina chatt-loggar från App Insights.
services: cognitive-services
author: diberry
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 5c55084a57e46931049841f5011941b2115e9e69
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961509"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Få analyser om din kunskapsbas

QnA Maker lagrar alla chatt-loggar och annan telemetri, om du har aktiverat App Insights när du [skapar din QNA Maker-tjänst](./set-up-qnamaker-service-azure.md). Kör exempel frågorna för att hämta dina chatt-loggar från App Insights.

1. Gå till din App Insights-resurs.

    ![Välj din Application Insights-resurs](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Välj **logg (analys)** . Ett nytt fönster öppnas där du kan fråga QnA Maker telemetri.

3. Klistra in i följande fråga och kör den.

    ```kusto
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration, performanceBucket
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
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
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Total fråga trafik under en viss tids period

```kusto
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Användar trafik

```kusto
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
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
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Välj capactiy](../tutorials/choosing-capacity-qnamaker-deployment.md)

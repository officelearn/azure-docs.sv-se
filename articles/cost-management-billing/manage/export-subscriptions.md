---
title: Exportera information på den översta nivån i din Azure-prenumeration
description: Här beskrivs hur du kan visa ID:n för alla Azure-prenumerationer som är kopplade till ditt konto.
author: bandersmsft
ms.reviewer: matrive
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b3b2e9b501f2ae103900a085e9b7a4b412efb78e
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686844"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Exportera och visa information på toppnivån för en prenumeration
Om du behöver visa de prenumerations-ID:n som är kopplade till din användare kan du [ladda ned en .json-fil med din prenumerationsinformation från Azure-kontocentret](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

Den nedladdade .json-filen innehåller följande information:
- Email: E-postadressen som är kopplad till ditt konto.
- Puid: Den unika identifierare som är kopplad till faktureringskontot.
- SubscriptionIds: En lista med prenumerationer som tillhör ditt konto, uppräknade efter prenumerations-ID.

### <a name="subscriptionsjson-sample"></a>subscriptions.json sample

```json
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
```

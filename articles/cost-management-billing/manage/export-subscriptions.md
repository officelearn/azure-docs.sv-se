---
title: Exportera information på den översta nivån i din Azure-prenumeration
description: Här beskrivs hur du kan visa ID:n för alla Azure-prenumerationer som är kopplade till ditt konto.
author: bandersmsft
ms.reviewer: matrive
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 87135b309f0406528bcada1cd906dd2f8535d1ae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202904"
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

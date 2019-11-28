---
title: Exportera information på toppnivån för din Azure-prenumeration | Microsoft Docs
description: Här beskrivs hur du kan visa ID:n för alla Azure-prenumerationer som är kopplade till ditt konto.
keywords: ''
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 87c31e663529ea20d119753094133941b02e2756
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226266"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Exportera och visa information på toppnivån för en prenumeration
Om du behöver visa de prenumerations-ID:n som är kopplade till din användare kan du [ladda ned en .json-fil med din prenumerationsinformation från Azure-kontocentret](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

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

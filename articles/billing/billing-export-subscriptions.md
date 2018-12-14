---
title: Exportera din Azure-prenumerationsinformation på toppnivån | Microsoft Docs
description: Beskriver hur du kan visa alla Azure-prenumerations-ID som är associerade med ditt konto.
keywords: ''
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: cwatson
ms.openlocfilehash: 09231ab69276f3b4763f07c51230921d15333f63
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339441"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Exportera och visa översta prenumerationsinformation
Om du vill visa en uppsättning prenumerations-ID som är associerade med dina användaruppgifter [hämta en .json-fil med din prenumerationsinformation från Azure Kontocenter](http://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Hämtade JSON-fil innehåller följande information:
- E-post: E-postadress som är associerade med ditt konto.
- PUID: Den unika identifieraren som är kopplade till ditt faktureringskonto.
- SubscriptionIds: En lista över prenumerationer som hör till ditt konto, av prenumerations-ID.

### <a name="subscriptionsjson-sample"></a>subscriptions.JSON exempel

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

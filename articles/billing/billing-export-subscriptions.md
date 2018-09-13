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
ms.author: adpick
ms.openlocfilehash: 7bc9f4df6e98dd86283c4389466b13b8f6bb4d15
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35962161"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Exportera och visa översta prenumerationsinformation
Om du vill visa en uppsättning prenumerations-ID som är associerade med dina användaruppgifter [hämta en .json-fil med din prenumerationsinformation från Azure Kontocenter](http://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Hämtade JSON-fil innehåller följande information:
- E-postadress: Det e-postadress som är associerade med ditt konto.
- PUID: Den unika identifieraren som är associerade med ditt faktureringskonto.
- SubscriptionIds: En lista över prenumerationer som hör till ditt konto, av prenumerations-ID.

### <a name="subscriptionsjson-sample"></a>subscriptions.JSON exempel
~~~~
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
~~~~

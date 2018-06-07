---
title: Exportera din Azure-prenumerationsinformation på toppnivån | Microsoft Docs
description: 'Beskriver hur du kan visa alla Azure-prenumeration ID: N som är kopplad till ditt konto.'
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
ms.date: 10/9/2017
ms.author: adpick
ms.openlocfilehash: b995b0c7154faab66a44bef1a7d74eeb8404e5c1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655455"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Exportera och se information om din översta prenumeration
Om du vill visa uppsättningen prenumerations-ID: N som är associerade med dina användaruppgifter [hämta en JSON-fil med din prenumerationsinformation från Azure Kontocenter](http://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Hämtade JSON-fil innehåller följande information:
- E-post: E-postadressen som är kopplad till ditt konto.
- PUID: Den unika identifieraren som är kopplad till ditt faktureringskonto.
- SubscriptionIds: Listan över prenumerationer som hör till ditt konto av prenumerations-ID.

### <a name="subscriptionsjson-sample"></a>subscriptions.JSON-exempel
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

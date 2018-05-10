---
title: Azure API management princip exempel - skicka begäran kontextinformation till serverdelstjänsten | Microsoft Docs
description: Azure API management princip exempel - visar hur du skickar sammanhangsinformation begäran till backend-tjänsten.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 932555a20ae89581b557d9d9f0a73992205d1e18
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="send-request-context-information-to-the-backend-service"></a>Skicka information om åtkomstbegäran kontext till serverdelstjänsten

Den här artikeln visar ett Azure API management princip exempel som visar hur du skickar sammanhangsinformation begäran till backend-tjänsten. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


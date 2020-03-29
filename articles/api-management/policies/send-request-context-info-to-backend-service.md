---
title: Exempel på API-hanteringsprincip - Skicka information om begärandekontext till serverdatjänsten
titleSuffix: Azure API Management
description: Exempel på Azure API-hanteringsprincip - Visar hur du skickar information om begärandekontext till serverdatjänsten.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 8cef989ca7ce8ee649c4f4aeb579c43bf8a8d6ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442411"
---
# <a name="send-request-context-information-to-the-backend-service"></a>Skicka begäran om sammanhangsinformation till serverdelstjänsten

Den här artikeln visar ett exempel på Azure API-hanteringsprincip som visar hur du skickar information om begärandekontext till serverdatjänsten. Om du vill ange eller redigera en principkod följer du stegen som beskrivs i [Ange eller redigerar en princip](../set-edit-policies.md). Mer om du vill se andra exempel finns i [principexempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Policyexempel](../policy-samples.md)


---
title: Exempel på API-hanteringsprincip - Flödesbegäran baserat på meddelandetextens storlek
titleSuffix: Azure API Management
description: Exempel på Azure API-hanteringsprincip - Visar hur du dirigerar begäranden baserat på storleken på deras kroppar.
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
ms.openlocfilehash: 8cee2c13386a076f0321619754468cfc1e9fb31c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442433"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Vidarebefordra begäran baserat på storleken på innehållet

Den här artikeln visar ett exempel på Azure API-hanteringsprincip som visar hur du dirigerar begäranden baserat på storleken på deras kroppar. Om du vill ange eller redigera en principkod följer du stegen som beskrivs i [Ange eller redigerar en princip](../set-edit-policies.md). Mer om du vill se andra exempel finns i [principexempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Policyexempel](../policy-samples.md)


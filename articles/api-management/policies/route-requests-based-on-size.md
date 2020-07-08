---
title: Exempel på API Management-princip – Route-begäran baserat på meddelande textens storlek
titleSuffix: Azure API Management
description: Exempel på Azure API Management policy – visar hur du dirigerar förfrågningar baserat på deras storlek.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75442433"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Vidarebefordra begäran baserat på storleken på innehållet

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du dirigerar förfrågningar baserat på storleken på deras organ. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip exempel](../policy-samples.md)


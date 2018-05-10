---
title: Azure API management princip exempel - vägen begäran baserat på storleken på innehållet | Microsoft Docs
description: Azure API management princip exemplet - visas hur du dirigera förfrågningar baserat på storleken på deras organ.
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
ms.openlocfilehash: da21a7c83a61b618646c4c4d2f422756b04dc300
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Vidarebefordra begäran baserat på storleken på innehållet

Den här artikeln visar ett Azure API management princip exempel som visar hur du dirigera förfrågningar baserat på storleken på deras organ. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


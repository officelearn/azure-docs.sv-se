---
title: Exempel-API Management-princip – auktorisera åtkomst baserat på JWT-anspråk
titleSuffix: Azure API Management
description: Exempel på Azure API Management policy – visar hur du auktoriserar åtkomst till vissa HTTP-metoder i ett API som baseras på JWT-anspråk.
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
ms.openlocfilehash: 1b84854688fbdcc017b16698dfebfe54a7210110
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75422185"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Auktorisera åtkomstbaserat på JWT-anspråk

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du auktoriserar åtkomst till vissa HTTP-metoder i ett API som baseras på JWT-anspråk. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip exempel](../policy-samples.md)


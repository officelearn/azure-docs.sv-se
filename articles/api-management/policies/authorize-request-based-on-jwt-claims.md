---
title: Exempel på API-hanteringsprincip – Auktorisera åtkomst baserat på JWT-anspråk
titleSuffix: Azure API Management
description: Exempel på Azure API-hanteringsprincip - Visar hur du auktoriserar åtkomst till specifika HTTP-metoder på ett API baserat på JWT-anspråk.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422185"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Auktorisera åtkomstbaserat på JWT-anspråk

Den här artikeln visar ett exempel på Azure API-hanteringsprincip som visar hur du auktoriserar åtkomst till specifika HTTP-metoder på ett API baserat på JWT-anspråk. Om du vill ange eller redigera en principkod följer du stegen som beskrivs i [Ange eller redigerar en princip](../set-edit-policies.md). Mer om du vill se andra exempel finns i [principexempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Policyexempel](../policy-samples.md)


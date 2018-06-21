---
title: Princip för hantering av Azure API exempel – auktorisera en begäran med externa authorizer | Microsoft Docs
description: Azure API management princip exemplet - visar hur godkänna begäranden med externa authorizer kapslar in en egen eller äldre autentisering/auktorisering logik.
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
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 7d172a40f2aad65b595026fc656634060a1f7193
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284880"
---
# <a name="authorize-requests-using-external-authorizer"></a>Tillåta begäranden som använder externa authorizer

Den här artikeln visar ett Azure API management princip exempel som visar hur du skyddar API-åtkomst med hjälp av en extern authorizer som kapslar in anpassad autentisering/auktorisering logik. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Principer för åtkomst-begränsningar](../api-management-access-restriction-policies.md)
+ [Princip-exempel](../policy-samples.md)
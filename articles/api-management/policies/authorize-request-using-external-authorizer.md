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
ms.openlocfilehash: cce2376c440c233d81198d903709867c5fdca7ec
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839887"
---
# <a name="authorize-requests-using-external-authorizer"></a>Tillåta begäranden som använder externa authorizer

Den här artikeln visar ett Azure API management princip exempel som visar hur du skyddar API-åtkomst med hjälp av en extern authorizer som kapslar in anpassad autentisering/auktorisering logik. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Principer för åtkomst-begränsningar](../api-management-access-restriction-policies.md)
+ [Princip-exempel](../policy-samples.md)
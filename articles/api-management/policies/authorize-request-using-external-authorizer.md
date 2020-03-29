---
title: Exempel på API-hanteringsprincip - Auktorisera begäran med extern auktoriserare
titleSuffix: Azure API Management
description: Exempel på Azure API-hanteringsprincip - Visar hur auktoriserar begäranden med hjälp av extern auktorisering som kapslar in en anpassad eller äldre autentiserings-/auktoriseringslogik.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 99bf1068042eb7ab0c43e2a683ca7116d2e426f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442488"
---
# <a name="authorize-requests-using-external-authorizer"></a>Auktorisera begäran med extern auktoriserare

Den här artikeln visar ett exempel på Azure API-hanteringsprincip som visar hur du skyddar API-åtkomst med hjälp av en extern auktoriseringsanpassning av anpassad autentiserings-/auktoriseringslogik. Om du vill ange eller redigera en principkod följer du stegen som beskrivs i [Ange eller redigerar en princip](../set-edit-policies.md). Mer om du vill se andra exempel finns i [principexempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Principer för åtkomstbegränsningar](../api-management-access-restriction-policies.md)
+ [Policyexempel](../policy-samples.md)

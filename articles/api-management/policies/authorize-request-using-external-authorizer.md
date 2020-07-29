---
title: Exempel-API Management-princip – auktorisera begäran med extern auktoriserare
titleSuffix: Azure API Management
description: Exempel på Azure API Management policy – visar hur auktoriserande begär Anden som använder extern auktoriserare kapslar in en anpassad eller äldre autentiserings-/auktoriserings logik.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75442488"
---
# <a name="authorize-requests-using-external-authorizer"></a>Auktorisera begäran med extern auktoriserare

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du skyddar API-åtkomst genom att använda en extern auktoriserare som kapslar in anpassad autentisering/auktoriserings logik. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Principer för åtkomst begränsningar](../api-management-access-restriction-policies.md)
+ [Princip exempel](../policy-samples.md)

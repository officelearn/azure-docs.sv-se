---
title: Azure API Management policy-exempel – auktorisera begäran med extern auktoriserare | Microsoft Docs
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
ms.openlocfilehash: 92836e0bfe43a41ad6547c68bc0b9a326528862c
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074156"
---
# <a name="authorize-requests-using-external-authorizer"></a>Auktorisera begär Anden med extern auktoriserare

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du skyddar API-åtkomst genom att använda en extern auktoriserare som kapslar in anpassad autentisering/auktoriserings logik. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Principer för åtkomst begränsningar](../api-management-access-restriction-policies.md)
+ [Princip exempel](../policy-samples.md)

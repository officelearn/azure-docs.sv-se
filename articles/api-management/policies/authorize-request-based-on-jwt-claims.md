---
title: Azure API management-princip exempel – auktorisera åtkomst baserat på JWT anspråk | Microsoft Docs
description: Azure API management princip-exempel – visar hur du tillåter åtkomst till specifika HTTP-metoder för API utifrån JWT-anspråk.
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
ms.openlocfilehash: 60b36ceeac1cd4578ca81ac908c1a8a03c9d0180
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869134"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Auktorisera åtkomst baserat på JWT-anspråk

Den här artikeln visar en Azure API management princip exempel som visar hur att bevilja åtkomst till specifika HTTP-metoder för API utifrån JWT-anspråk. Om du vill ange eller redigera en princip för kod, följer du stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Mer information om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


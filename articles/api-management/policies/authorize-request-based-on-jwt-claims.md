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
ms.openlocfilehash: d656cf7c7bed1d40bbde654f9c2484efcc5df25d
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157929"
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


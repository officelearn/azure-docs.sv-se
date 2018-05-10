---
title: Princip för hantering av Azure API exempel – auktorisera åtkomst med hjälp av Google OAuth-token | Microsoft Docs
description: Azure API management princip exemplet - visas hur att bevilja åtkomst till dina slutpunkter med Google som en OAuth-token-provider.
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
ms.openlocfilehash: 46ffe21b31597f889b411a40a9f3e1f2dd76f1ea
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="authorize-access-using-google-oauth-token"></a>Auktorisera åtkomst med hjälp av Google OAuth-token

Den här artikeln visar ett Azure API management princip exempel som visar hur att bevilja åtkomst till dina slutpunkter med Google som en OAuth-token-provider. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


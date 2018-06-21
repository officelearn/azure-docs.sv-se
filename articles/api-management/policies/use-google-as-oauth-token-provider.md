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
ms.openlocfilehash: 22aed976ef69288aa0e49215a739174786843527
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284598"
---
# <a name="authorize-access-using-google-oauth-token"></a>Auktorisera åtkomst med hjälp av Google OAuth-token

Den här artikeln visar ett Azure API management princip exempel som visar hur att bevilja åtkomst till dina slutpunkter med Google som en OAuth-token-provider. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


---
title: Exempel-API Management-princip – auktorisera åtkomst med Google OAuth-token
titleSuffix: Azure API Management
description: Exempel på Azure API Management-princip – visar hur du auktoriserar åtkomst till dina slut punkter med Google som en OAuth-token-Provider.
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
ms.openlocfilehash: d606d29d84cd5917c74efe188ae02627ad55d4ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "75442378"
---
# <a name="authorize-access-using-google-oauth-token"></a>Auktorisera åtkomst med hjälp av Google OAuth-token

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du auktoriserar åtkomst till dina slut punkter med Google som en OAuth-token-Provider. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip exempel](../policy-samples.md)


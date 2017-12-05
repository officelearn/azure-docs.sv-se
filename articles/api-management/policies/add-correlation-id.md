---
title: "Exempel på princip för hantering av Azure API - lägga till ett sidhuvud som innehåller en Korrelations-id | Microsoft Docs"
description: "Azure API management princip exemplet - visar hur du lägger till en rubrik som innehåller en Korrelations-id till en inkommande begäran."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: a550009b4442bb59b9b9f4b18593a7537213bb78
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="add-a-header-containing-a-correlation-id"></a>Lägga till ett sidhuvud som innehåller en Korrelations-id

Den här artikeln visar ett Azure API management princip exempel som visar hur du lägger till en rubrik som innehåller en Korrelations-id till en inkommande begäran. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


---
title: "Princip för hantering av Azure API exempel – Lägg till funktioner till en backend-tjänst | Microsoft Docs"
description: "Azure API management princip exemplet - visas hur du lägger till funktioner till en backend-tjänst. Godkänn exempelvis ett namn på plats i stället för latitud och longitud i en väder prognos API."
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
ms.openlocfilehash: d500df0f0e48134ac9c1397795d65706d2e56ff9
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="add-capabilities-to-a-backend-service"></a>Lägga till funktioner i en serverdelstjänst

Den här artikeln visar ett Azure API management princip exempel som visar hur du lägger till funktioner till en backend-tjänst. Godkänn exempelvis ett namn på plats i stället för latitud och longitud i en väder prognos API. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


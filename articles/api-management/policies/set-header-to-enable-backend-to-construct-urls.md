---
title: "Exempel på princip för hantering av Azure API - lägga till ett sidhuvud vidarebefordrade | Microsoft Docs"
description: "Azure API management princip exemplet - visar hur du lägger till en vidarebefordrade rubrik i en inkommande begäran att serverdelen API för att skapa rätt URL: er."
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
ms.openlocfilehash: cc2df914532b6cda37c951b65b243e90b63d57cb
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="add-a-forwarded-header"></a>Lägga till ett sidhuvud vidarebefordrade

Den här artikeln visar ett Azure API management princip exempel som visar hur du lägger till en vidarebefordrade rubrik i en inkommande begäran att serverdelen API för att skapa rätt URL: er. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="code"></a>Kod

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)

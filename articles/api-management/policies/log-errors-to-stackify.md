---
title: "Azure API management princip exempel - skicka fel till Stackify för loggning | Microsoft Docs"
description: "Azure API management princip exemplet - visar hur du lägger till en princip för loggning av fel om du vill skicka fel till Stackify för loggning..."
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
ms.openlocfilehash: 34319e43789d0f2c7d3e48a0277926eb598e88fc
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="send-errors-to-stackify-for-logging"></a>Skicka fel till Stackify för loggning

Den här artikeln visar ett Azure API management princip exempel som visar hur du lägger till en princip för loggning av fel om du vill skicka fel till Stackify för loggning. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **vid fel** block.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


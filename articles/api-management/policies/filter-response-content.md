---
title: "Azure API management princip exempel - svar filterinnehållet | Microsoft Docs"
description: "Azure API management princip exemplet - visas hur du filtrerar dataelement från svar nyttolasten baserat på produkten som är kopplad till begäran."
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
ms.openlocfilehash: 2acaad9e97f18cca22893c8c41ead4533ac5ae85
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="filter-response-content"></a>Filtrera svar innehåll

Den här artikeln visar ett Azure API management princip exempel som visar hur du filtrerar dataelement från svar nyttolasten baserat på produkten som är kopplad till begäran. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **utgående** block.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


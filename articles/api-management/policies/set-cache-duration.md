---
title: "Azure API management princip exempel - ange varaktighet för svar på cache | Microsoft Docs"
description: "Azure API management princip exempel - visar hur du anger varaktighet för svar på cache med hjälp av maxAge värdet i Cache-Control-huvudet som skickas av serverdelen..."
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
ms.openlocfilehash: 8471b51999159c8f6233abbbce998b122163efd8
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="set-response-cache-duration"></a>Ange varaktighet för svar på cache

Den här artikeln visar ett Azure API management princip exempel som visar hur du ställer in varaktighet för svar på cache med hjälp av maxAge värdet i Cache-Control-huvudet som skickas av serverdelen. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


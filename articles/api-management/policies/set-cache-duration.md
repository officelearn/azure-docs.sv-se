---
title: Azure API management princip-exempel – ange svar cachevaraktighet | Microsoft Docs
description: Azure API management princip-exempel – visar hur du ställer svar cachelagringens varaktighet med maxAge värdet i Cache-Control-huvudet som skickas av serverdelen...
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
ms.openlocfilehash: 042fab72da2d4b890314b6ee9c7237241b492fba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859166"
---
# <a name="set-response-cache-duration"></a>Ange svar cachelagringens varaktighet

Den här artikeln visar en Azure API management princip exempel som visar hur du ställer in svar cachelagringens varaktighet med maxAge värdet i Cache-Control-huvudet som skickas av serverdelen. Om du vill ange eller redigera en princip för kod, följer du stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Mer information om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


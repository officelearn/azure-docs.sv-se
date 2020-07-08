---
title: Exempel-API Management-princip – ange varaktighet för svars-cache
titleSuffix: Azure API Management
description: Exempel på Azure API Management policy-visar hur du ställer in varaktigheten för svars-cachen med maxAge-värdet i Cache-Control-huvudet som skickas av Server delen.
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
ms.openlocfilehash: 3101c5695272e8fa6b577ad313897cbc1fa29629
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75442401"
---
# <a name="set-response-cache-duration"></a>Ange varaktighet för svarscache

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du anger varaktighet för svars-cachen med maxAge-värdet i Cache-Control-huvudet som skickas av Server delen. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip exempel](../policy-samples.md)


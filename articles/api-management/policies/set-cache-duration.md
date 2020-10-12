---
title: Exempel-API Management-princip – ange varaktighet för svars-cache
titleSuffix: Azure API Management
description: Exempel på Azure API Management policy-visar hur du ställer in varaktighet för svars-cache med ett maxAge-värde i Cache-Control rubrik som skickats av Server delen..
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "75442401"
---
# <a name="set-response-cache-duration"></a>Ange varaktighet för svarscache

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du ställer in varaktigheten för svars cachen med ett maxAge-värde i Cache-Control rubriken som skickas av Server Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip exempel](../policy-samples.md)


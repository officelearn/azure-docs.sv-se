---
title: Exempel på API-hanteringsprincip - Ange varaktighet för svarscachen
titleSuffix: Azure API Management
description: Exempel på Azure API-hanteringsprincip - Visar hur du anger svarscachevaraktighet med maxAge-värdet i cachekontrollhuvudet som skickas av serverda..
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442401"
---
# <a name="set-response-cache-duration"></a>Ange varaktighet för svarscache

Den här artikeln visar ett exempel på Azure API-hanteringsprincip som visar hur du anger svarscachevaraktighet med maxAge-värdet i cachekontrollhuvudet som skickas av serverda. Om du vill ange eller redigera en principkod följer du stegen som beskrivs i [Ange eller redigerar en princip](../set-edit-policies.md). Mer om du vill se andra exempel finns i [principexempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Policyexempel](../policy-samples.md)


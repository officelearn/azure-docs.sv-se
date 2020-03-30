---
title: Exempel på API-hanteringsprincip - Lägg till funktioner i serverdatjänsten
titleSuffix: Azure API Management
description: Exempel på Azure API-hanteringsprincip - Visar hur du lägger till funktioner i en serverd-tjänst. Godkänn exempelvis ett namn på platsen i stället för latitud och longitud i en väderprognos-API.
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
ms.openlocfilehash: e286f77009a17870332ed5caf1c66c18dd21d020
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442475"
---
# <a name="add-capabilities-to-a-backend-service"></a>Lägga till funktioner i en backend-tjänst

Den här artikeln visar ett exempel på Azure API-hanteringsprincip som visar hur du lägger till funktioner i en serverd-tjänst. Godkänn exempelvis ett namn på platsen i stället för latitud och longitud i en väderprognos-API. Om du vill ange eller redigera en principkod följer du stegen som beskrivs i [Ange eller redigerar en princip](../set-edit-policies.md). Mer om du vill se andra exempel finns i [principexempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Policyexempel](../policy-samples.md)


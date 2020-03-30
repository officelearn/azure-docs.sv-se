---
title: Exempel på API-hanteringsprincip - Lägg till ett huvud som innehåller korrelations-ID
titleSuffix: Azure API Management
description: Exempel på Azure API-hanteringsprincip - Visar hur du lägger till ett huvud som innehåller ett korrelations-ID till den inkommande begäran.
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
ms.openlocfilehash: 79910aa60602a80cbe79c4ce9899f6ff73fbfde9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422255"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Lägga till en rubrik som innehåller ett korrelations-id

Den här artikeln visar ett exempel på Azure API-hanteringsprincip som visar hur du lägger till ett huvud som innehåller ett korrelations-ID till den inkommande begäran. Om du vill ange eller redigera en principkod följer du stegen som beskrivs i [Ange eller redigerar en princip](../set-edit-policies.md). Mer om du vill se andra exempel finns i [principexempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Policyexempel](../policy-samples.md)


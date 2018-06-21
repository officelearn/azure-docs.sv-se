---
title: Exempel på princip för hantering av Azure API - lägga till ett sidhuvud som innehåller en Korrelations-id | Microsoft Docs
description: Azure API management princip exemplet - visar hur du lägger till en rubrik som innehåller en Korrelations-id till en inkommande begäran.
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
ms.openlocfilehash: 68f42124369194124ae1f8ebb93834a5be4e0128
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287385"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Lägga till ett sidhuvud som innehåller en Korrelations-id

Den här artikeln visar ett Azure API management princip exempel som visar hur du lägger till en rubrik som innehåller en Korrelations-id till en inkommande begäran. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


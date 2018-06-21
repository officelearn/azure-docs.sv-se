---
title: Princip för hantering av Azure API exempel – Lägg till funktioner till en backend-tjänst | Microsoft Docs
description: Azure API management princip exemplet - visas hur du lägger till funktioner till en backend-tjänst. Godkänn exempelvis ett namn på platsen i stället för latitud och longitud i en väderprognos-API.
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
ms.openlocfilehash: a019eb4556dc7cde34d51af6858f576e8ea9abcf
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284539"
---
# <a name="add-capabilities-to-a-backend-service"></a>Lägga till funktioner i en serverdelstjänst

Den här artikeln visar ett Azure API management princip exempel som visar hur du lägger till funktioner till en backend-tjänst. Godkänn exempelvis ett namn på platsen i stället för latitud och longitud i en väderprognos-API. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


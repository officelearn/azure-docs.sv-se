---
title: Exempel på Azure API Management-princip – lägga till funktioner i en backend-tjänst | Microsoft Docs
description: Exempel på Azure API Management policy – visar hur du lägger till funktioner i en backend-tjänst. Godkänn exempelvis ett namn på platsen i stället för latitud och longitud i en väderprognos-API.
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
ms.openlocfilehash: 705d7e44f64f8dc3bba669cd80dafdab078fcccc
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067779"
---
# <a name="add-capabilities-to-a-backend-service"></a>Lägga till funktioner i en backend-tjänst

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du lägger till funktioner i en backend-tjänst. Godkänn exempelvis ett namn på platsen i stället för latitud och longitud i en väderprognos-API. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip exempel](../policy-samples.md)


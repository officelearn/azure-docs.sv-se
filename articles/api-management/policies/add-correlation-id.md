---
title: Exempel på Azure API Management-princip – Lägg till en rubrik som innehåller ett korrelations-ID | Microsoft Docs
description: Exempel på Azure API Management policy – visar hur du lägger till en rubrik som innehåller ett korrelations-ID till den inkommande begäran.
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
ms.openlocfilehash: 9f091345a4eaf174c47959cad3cb4525fd926689
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074212"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Lägg till en rubrik som innehåller ett korrelations-ID

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du lägger till en rubrik som innehåller ett korrelations-ID till den inkommande begäran. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip exempel](../policy-samples.md)


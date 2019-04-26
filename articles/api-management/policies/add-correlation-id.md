---
title: Exempel på princip för hantering av Azure API - Lägg till en rubrik som innehåller ett Korrelations-id | Microsoft Docs
description: Azure API management princip exempel - visar hur du lägger till en rubrik som innehåller ett Korrelations-id på inkommande begäran.
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
ms.openlocfilehash: 126701674b6dc529404f5a7854cda9b31c336170
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306791"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Lägga till ett huvud som innehåller ett Korrelations-id

Den här artikeln visar en Azure API management princip exempel som visar hur du lägger till en rubrik som innehåller ett Korrelations-id på inkommande begäran. Om du vill ange eller redigera en princip för kod, följer du stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Mer information om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


---
title: Exempel för Azure API-princip för hantering – skicka fel till Stackify för loggning | Microsoft Docs
description: Azure API management princip exempel - visar hur du lägger till en princip för loggning av fel om du vill skicka fel till Stackify för loggning...
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
ms.openlocfilehash: 07cc83830fe2d467c611622bb66dfbb8c9429c2d
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52864884"
---
# <a name="send-errors-to-stackify-for-logging"></a>Skicka fel till Stackify för loggning

Den här artikeln visar ett exempel på Azure API management-princip som visar hur du lägger till en princip för loggning av fel om du vill skicka fel till Stackify för loggning. Om du vill ange eller redigera en princip för kod, följer du stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **vid fel** block.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Mer information om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


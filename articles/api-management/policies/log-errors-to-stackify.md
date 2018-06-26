---
title: Azure API management princip exempel - skicka fel till Stackify för loggning | Microsoft Docs
description: Azure API management princip exemplet - visar hur du lägger till en princip för loggning av fel om du vill skicka fel till Stackify för loggning...
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
ms.openlocfilehash: 5daf21cb55289c874d56910b1240e1433f3d55d0
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2018
ms.locfileid: "36945963"
---
# <a name="send-errors-to-stackify-for-logging"></a>Skicka fel till Stackify för loggning

Den här artikeln visar ett Azure API management princip exempel som visar hur du lägger till en princip för loggning av fel om du vill skicka fel till Stackify för loggning. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **vid fel** block.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


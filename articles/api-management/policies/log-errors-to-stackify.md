---
title: Exempel på API-hanteringsprincip - Skicka fel till Stackify för loggning
titleSuffix: Azure API Management
description: Exempel på Azure API-hanteringsprincip - Visar hur du lägger till en princip för felloggning för att skicka fel till Stackify för loggning..
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
ms.openlocfilehash: 6662761df005211729dffb16282b8e0a8e2a8444
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442445"
---
# <a name="send-errors-to-stackify-for-logging"></a>Skicka fel till Stackify för loggning

Den här artikeln visar ett exempel på Azure API-hanteringsprincip som visar hur du lägger till en princip för felloggning för att skicka fel till Stackify för loggning. Om du vill ange eller redigera en principkod följer du stegen som beskrivs i [Ange eller redigerar en princip](../set-edit-policies.md). Mer om du vill se andra exempel finns i [principexempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden **on-error** i felblocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Policyexempel](../policy-samples.md)


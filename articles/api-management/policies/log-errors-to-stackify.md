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
ms.openlocfilehash: 46b6d391d6a1ee569dc27c31a0718b23a120c632
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286726"
---
# <a name="send-errors-to-stackify-for-logging"></a>Skicka fel till Stackify för loggning

Den här artikeln visar ett Azure API management princip exempel som visar hur du lägger till en princip för loggning av fel om du vill skicka fel till Stackify för loggning. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **vid fel** block.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


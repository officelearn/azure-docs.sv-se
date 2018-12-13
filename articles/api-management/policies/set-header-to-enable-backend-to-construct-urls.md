---
title: Exempel på princip för hantering av Azure API - Lägg till en rubrik för vidarebefordrade | Microsoft Docs
description: 'Azure API management princip exempel - visar hur du lägger till en rubrik för vidarebefordrade i inkommande begäran för att tillåta serverdels-API att konstruera rätt URL: er.'
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
ms.openlocfilehash: b857d1780e9734ce891ce2c0ce4bedf50dfe13e9
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871463"
---
# <a name="add-a-forwarded-header"></a>Lägga till ett vidarebefordrade-huvud

Den här artikeln visar en Azure API management princip exempel som visar hur du lägger till en rubrik för vidarebefordrade i inkommande begäran för att tillåta serverdels-API att konstruera rätt URL: er. Om du vill ange eller redigera en princip för kod, följer du stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns i [princip exempel](../policy-samples.md).

## <a name="code"></a>Kod

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Mer information om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)

---
title: Exempel på princip för hantering av Azure API - lägga till ett sidhuvud vidarebefordrade | Microsoft Docs
description: 'Azure API management princip exemplet - visar hur du lägger till en vidarebefordrade rubrik i en inkommande begäran att serverdelen API för att skapa rätt URL: er.'
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
ms.openlocfilehash: a9dfcbf4be4b659d761d66d67d2ae4c7b70a245e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284172"
---
# <a name="add-a-forwarded-header"></a>Lägga till ett sidhuvud vidarebefordrade

Den här artikeln visar ett Azure API management princip exempel som visar hur du lägger till en vidarebefordrade rubrik i en inkommande begäran att serverdelen API för att skapa rätt URL: er. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="code"></a>Kod

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)

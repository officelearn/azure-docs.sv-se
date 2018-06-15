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
ms.openlocfilehash: 00c8ac567b476d0591069c83c371d987d651de9d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33935098"
---
# <a name="add-a-forwarded-header"></a>Lägga till ett sidhuvud vidarebefordrade

Den här artikeln visar ett Azure API management princip exempel som visar hur du lägger till en vidarebefordrade rubrik i en inkommande begäran att serverdelen API för att skapa rätt URL: er. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="code"></a>Kod

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)

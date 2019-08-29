---
title: Hanterings princip exempel för Azure API – ange varaktighet för svars-cachen | Microsoft Docs
description: Exempel på Azure API Management policy-visar hur du ställer in varaktigheten för svars-cachen med maxAge-värdet i Cache-Control-huvudet som skickas av Server delen.
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
ms.openlocfilehash: 67d2f334e2088d96543fabd3b99cac71d95630eb
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071972"
---
# <a name="set-response-cache-duration"></a>Ange varaktighet för svars-cache

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du anger varaktighet för svars-cachen med maxAge-värdet i Cache-Control-huvudet som skickas av Server delen. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip exempel](../policy-samples.md)


---
title: Azure API management princip exempel – implementera X-CSRF mönstret | Microsoft Docs
description: 'Azure API management princip exempel - visar hur du implementerar X CSRF mönstret som används av många API: er. Det här exemplet är specifikt för SAP-gateway.'
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
ms.openlocfilehash: 2f4d26702443ef3113dad98cde1d13b292fe657a
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52870018"
---
# <a name="implement-x-csrf-pattern"></a>Implementera X-CSRF-mönster

Den här artikeln visar ett urval av Azure API management-princip som visar hur du implementerar X CSRF mönstret som används av många API: er. Det här exemplet är specifikt för SAP-gateway. Om du vill ange eller redigera en princip för kod, följer du stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Mer information om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


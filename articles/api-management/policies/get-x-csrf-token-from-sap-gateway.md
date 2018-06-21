---
title: Azure API management princip exempel - implementera X-CSRF mönster | Microsoft Docs
description: 'Azure API management princip exemplet - visar hur du implementerar X CSRF mönster som används av många API: er. Det här exemplet är specifikt för SAP-gateway.'
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
ms.openlocfilehash: 2e9f51cc2afadf70c02a23a85a4c2a866f3fd149
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285478"
---
# <a name="implement-x-csrf-pattern"></a>Implementera X-CSRF mönster

Den här artikeln visar ett Azure API management princip exempel som visar hur du implementerar X CSRF mönster som används av många API: er. Det här exemplet är specifikt för SAP-gateway. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


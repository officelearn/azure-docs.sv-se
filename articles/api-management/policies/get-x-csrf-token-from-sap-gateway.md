---
title: Exempel på Azure API-hanteringsprincip – Implementera X-CSRF-mönster | Microsoft-dokument
description: Exempel på Azure API-hanteringsprincip - Visar hur du implementerar X-CSRF-mönster som används av många API:er. Det här exemplet är specifikt för SAP-gateway.
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
ms.openlocfilehash: 14ea9113bf5712d6ffce356d02abb7224c21771a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70067726"
---
# <a name="implement-x-csrf-pattern"></a>Implementera X-CSRF-mönster

Den här artikeln visar ett exempel på Azure API-hanteringsprincip som visar hur du implementerar X-CSRF-mönster som används av många API:er. Det här exemplet är specifikt för SAP-gateway. Om du vill ange eller redigera en principkod följer du stegen som beskrivs i [Ange eller redigerar en princip](../set-edit-policies.md). Mer om du vill se andra exempel finns i [principexempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Policyexempel](../policy-samples.md)


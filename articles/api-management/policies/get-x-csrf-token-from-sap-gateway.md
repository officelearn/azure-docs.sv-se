---
title: Exempel på Azure API Management policy – implementera X-CSRF-mönster | Microsoft Docs
description: 'Exempel på Azure API Management policy-visar hur du implementerar CSRF-mönster som används av många API: er. Det här exemplet är specifikt för SAP-gateway.'
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "70067726"
---
# <a name="implement-x-csrf-pattern"></a>Implementera X-CSRF-mönster

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du implementerar X-CSRF-mönster som används av många API: er. Det här exemplet är specifikt för SAP-gateway. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Policy

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip exempel](../policy-samples.md)


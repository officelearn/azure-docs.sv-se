---
title: Exempel på API-hanteringsprincip - Filtrera på IP-adress när du använder Application Gateway
titleSuffix: Azure API Management
description: Exempel på Azure API-hanteringsprincip - Visar hur du filtrerar på IP-adress för begäran när du använder en Application Gateway.
services: api-management
documentationcenter: ''
author: jftl6y
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 45e16c9aa9e4b04e7225320951e9f839fae75ba3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75942482"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Filtrera på IP-adress för begäran när du använder en programgateway

Den här artikeln visar ett exempel på Azure API-hanteringsprincip som visar hur filtret på IP-adressen för begäran när API Management-instansen nås via en Application Gateway eller annan mellanhand. Om du vill ange eller redigera en principkod följer du stegen som beskrivs i [Ange eller redigerar en princip](../set-edit-policies.md). Mer om du vill se andra exempel finns i [principexempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Principer för åtkomstbegränsningar](../api-management-access-restriction-policies.md)
+ [Policyexempel](../policy-samples.md)

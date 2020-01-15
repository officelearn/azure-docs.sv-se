---
title: Exempel på API Management-princip – filter på IP-adress när du använder Application Gateway
titleSuffix: Azure API Management
description: Exempel på Azure API Management-princip – visar hur du filtrerar IP-adresser för begäran när du använder en Application Gateway.
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
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942482"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Filtrera på begär IP-adress när du använder en Application Gateway

I den här artikeln visas ett exempel på en Azure API Management-princip som visar hur filtret på IP-adressen för begäran när API Management-instansen nås via en Application Gateway eller annan mellanhand. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Principer för åtkomst begränsningar](../api-management-access-restriction-policies.md)
+ [Princip exempel](../policy-samples.md)

---
title: Exempel på Azure API-hanteringsprincip – Lägg till ett vidarebefordrat huvud | Microsoft-dokument
description: Exempel på Azure API-hanteringsprincip - Visar hur du lägger till ett vidarebefordrat huvud i den inkommande begäran så att serverd-API:et kan skapa rätt webbadresser.
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
ms.openlocfilehash: e525029ae8eab086d11126a4e18958423e207aa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70067503"
---
# <a name="add-a-forwarded-header"></a>Lägga till ett vidarebefordrat huvud

Den här artikeln visar ett exempel på Azure API-hanteringsprincip som visar hur du lägger till ett vidarebefordrat huvud i den inkommande begäran så att serverd-API:et kan skapa rätt webbadresser. Om du vill ange eller redigera en principkod följer du stegen som beskrivs i [Ange eller redigerar en princip](../set-edit-policies.md). Mer om du vill se andra exempel finns i [principexempel](../policy-samples.md).

## <a name="code"></a>Kod

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Policyexempel](../policy-samples.md)

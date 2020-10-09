---
title: Exempel på Azure API Management-princip – Lägg till en vidarebefordrad rubrik | Microsoft Docs
description: 'Exempel på Azure API Management-princip – visar hur du lägger till en vidarebefordrad rubrik i den inkommande begäran för att tillåta Server dels-API: et att skapa korrekta URL: er.'
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "70067503"
---
# <a name="add-a-forwarded-header"></a>Lägg till en vidarebefordrad rubrik

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du lägger till en vidarebefordrad rubrik i den inkommande begäran för att tillåta Server dels-API: et att skapa korrekta URL: er. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-samples.md).

## <a name="code"></a>Kod

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip exempel](../policy-samples.md)

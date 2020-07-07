---
title: Azure API Management policy exempel – filter svar innehåll | Microsoft Docs
description: Exempel på Azure API Management policy – visar hur du filtrerar data element från nytto lasten för svar baserat på den produkt som är associerad med begäran.
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
ms.openlocfilehash: 462db76b06e5071571ae475d420a627e57dad92e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "70067759"
---
# <a name="filter-response-content"></a>Filtrera svarsinnehåll

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du filtrerar data element från svarets nytto Last baserat på den produkt som är associerad med begäran. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Policy

Klistra in koden i det **utgående** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip exempel](../policy-samples.md)


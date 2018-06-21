---
title: Azure API management princip exempel - svar filterinnehållet | Microsoft Docs
description: Azure API management princip exemplet - visas hur du filtrerar dataelement från svar nyttolasten baserat på produkten som är kopplad till begäran.
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
ms.openlocfilehash: af362ac51fb8b7d1689451d49f2ed831c5f9ee2e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284435"
---
# <a name="filter-response-content"></a>Filtrera svar innehåll

Den här artikeln visar ett Azure API management princip exempel som visar hur du filtrerar dataelement från svar nyttolasten baserat på produkten som är kopplad till begäran. Om du vill ange eller redigera en princip-kod, Följ stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **utgående** block.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om APIM principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Princip-exempel](../policy-samples.md)


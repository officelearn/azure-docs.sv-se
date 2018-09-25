---
title: Azure API management-princip exempel – godkänna begäran med hjälp av externa authorizer | Microsoft Docs
description: Azure API management princip exempel – visar hur godkänna begäranden med hjälp av externa authorizer som kapslar in en logik som anpassade eller äldre autentisering/auktorisering.
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
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 70f43a058cfd3818dae1ccfa4b222a7d0d740aee
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979411"
---
# <a name="authorize-requests-using-external-authorizer"></a>Godkänna begäranden med hjälp av externa authorizer

Den här artikeln visar en Azure API management princip exempel som visar hur du skyddar API-åtkomst med hjälp av en extern authorizer som kapslar in autentisering/auktorisering med anpassad logik. Om du vill ange eller redigera en princip för kod, följer du stegen som beskrivs i [ange eller redigera en princip](../set-edit-policies.md). Andra exempel finns i [princip exempel](../policy-samples.md).

## <a name="policy"></a>Princip

Klistra in koden i den **inkommande** block.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Mer information om APIM-principer:

+ [Åtkomstprinciper för begränsningar](../api-management-access-restriction-policies.md)
+ [Princip-exempel](../policy-samples.md)
